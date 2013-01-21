---
layout: page
title : Code Snippets
header : Code Snippets
group: navigation
---

## Code Snippets

> Some PHP snippets, which might (or might not) be useful for you. Use at own risk!

1. [Recursive directory listing](#recursive-dir)
2. [Limited output for directories](#limit-dir)
3. [Check for valentine's day](#valentine)
4. [A simple calendar](#calendar)
5. [Limited output in mysql (Pagination)](#limit-mysql)
6. [Formatted output with tidy](#tidy)



<a id="recursive-dir"></a>

### Recursive directory listing

{% highlight php %}
<?php
// Directory to read from
$dir = './';
session_start();
$absoluteUrl = 'http://'. $_SERVER['HTTP_HOST'] . htmlspecialchars($_SERVER['PHP_SELF']);
if(isset($_GET['closeAll']) && $_GET['closeAll'] == '1') {
    unset($_SESSION['dir']);
}
$_SESSION['dir'] = (isset($_SESSION['dir'])) ? $_SESSION['dir'] : array();

if(isset($_GET['dir'])) {
    $key = array_search($_GET['dir'], $_SESSION['dir']);
    if($key !== false) {
        unset($_SESSION['dir'][$key]);
    } else {
        $_SESSION['dir'][] = $_GET['dir'];
    }

    header("Location: http://". $_SERVER['HTTP_HOST'] . dirname($_SERVER['PHP_SELF']) .'/'. basename($_SERVER['PHP_SELF']) .'#'. $_GET['dir']);
}

function recDir($iterator)
{
    static $depth = 0;
    $depth++;

    foreach($iterator as $value) {
        $hash = md5($iterator->getPathName());
        if(is_numeric($hash{0})) {
            $hash = "a". $hash;
        }

        if($iterator->isDir()) {
            echo str_repeat('---', $depth) .'<a href="'. $absoluteUrl .'?dir='. $hash .'" id="'. $hash .'">'. $value ."</a> (Verzeichnis)<br />\n";
        } else {
            echo str_repeat('---', $depth) .'<a href="'. $absoluteUrl .'?filename='. urlencode($iterator->getPathName()) .'" id="'. $hash .'">'. $value ."</a><br />\n";
        }

        if($iterator->hasChildren() && in_array($hash, $_SESSION['dir'])) {
            recDir($iterator->getChildren());
        }
    }

    $depth--;
}
echo '<style type="text/css">a { color: blue; text-decoration: none; } a:hover {color: orange; text-decoration: underline;}</style>';
echo '<a href="'. $absoluteUrl .'?closeAll=1">Close all directories</a><br /><br />';

$foo = new RecursiveDirectoryIterator($dir);
echo '  <div style="float: left; width: 25em;">';
recDir($foo);
echo "</div>";
// Restrict directory access
$filename = (isset($_GET['filename'])) ? str_replace('..', '/', $_GET['filename']) : false;
$filename = ($filename{0} == '.') ? $filename : '.'. $filename;
if(($filename !== false) && file_exists($filename) && is_file($filename)) {
    echo $filename;
    ?>
    <form method="post">
        <textarea cols="100" rows="30"><?php echo htmlspecialchars(file_get_contents($filename)); ?></textarea>
    </form>
    <?php
}
{% endhighlight %}

<a id="limit-dir"></a>
### Limited output for directories


{% highlight php %}
<?php
$foo = new DirectoryIterator('./');
$page_s = (isset($_GET['page'])) ? $_GET['page'] : 1;
$number = 0;
foreach($foo as $key => $val) {
    if(!$val->isDot()) {
        $number++;
    }
}

$page = (isset($_GET['page'])) ? abs((int)$_GET['page']) - 1 : 0;

$filesPerSite = 10;
$offset       = $page * $filesPerSite;
$numberOfPages = ceil($number / $filesPerSite);

if($offset == 0) {
    $offset = 2;
}

$limit = new LimitIterator($foo, $offset, $filesPerSite);

// Output files and directories
foreach($limit as $value) {
    echo $value ."<br />";
}

// Pagination
for($i = 1; $i <= $numberOfPages; $i++) {
    if($i == (abs($page_s))) {
        echo " <strong>[". $i ."]</strong> ";
    } else {
        echo ' <a href="?page='. $i .'">'. $i .'</a>';
    }
}
{% endhighlight %}

<a id="valentine"></a>
### Check for valentines day

**Note**
> This nonsense was completely written by Slashlife (from the IRC-Network QuakeNet). All glory to Slashlife :)

{% highlight php %}
<?php

$o='check for valentines day';
$o=$o{(1>>3)+(3|1)}.$o{(1>>3)|(3>>1)}.$o{(1|(3*3))-1};
$o=$o((13<<(3*1))^13^(3&1)).$o(((13^(3&1))<<(1|3))|(3>>1)) . $o(((13^3)<<1)*(1+3)+3+1).$o((13<<(3|1))^(13|(3+1)));

if ($o($o((13<<(3*1))+(1*(3<<(3>>1)))).$o((13<<(3|1))^13^(3&1)))== (13<<(3+1))+(13>>(3>>1))) {
    echo 'Happy Valentines Day!';
} else {
    echo 'Try again tomorrow...';
}
{% endhighlight %}

<a id="calendar"></a>
### A simple calendar (maybe the simplest you have ever seen - use at own risk)

{% highlight php %}
<?php
$month  = (isset($_GET['month'])) ? (int)$_GET['month'] : date("m");
$year   = date("Y");
$day    = date("d");
$first_day_timestamp    = mktime(0, 0, 1, $month, 1, $year);
$first_weekday          = date("w", $first_day_timestamp);
$last_day_timestamp     = mktime(0, 0, 1, $month, date("t", $first_day_timestamp), $year);
$last_weekday           = date("w", $last_day_timestamp);
$array                  = array('Mo.', 'Di.', 'Mi.', 'Do.', 'Fr.', 'Sa.', 'So.');
$html                   = '<pre>'."\t\t    ".'<a href="'. htmlspecialchars($_SERVER['PHP_SELF']) .'?month='. ($month-1) .'"><<</a>'."\t    ";
$html                  .= '<a href="'. htmlspecialchars($_SERVER['PHP_SELF']) .'?month='. ($month+1) .'">>></a>'."\n\n";
$html                  .= "\t\t    ". date("M", $first_day_timestamp) .' - '. date("Y", $first_day_timestamp) ."\n";
for($i = 0; $i < 7; $i++) {
    $html .= '<span>'. $array[$i] ."</span>\t";
}
$html  .= "\n";
$num    = 1;
$start  = ($first_weekday != 0) ? 2-$first_weekday : -5;
$last   = date("t", $first_day_timestamp) +(8-$last_weekday);
$last   = (($last - 8) == date("t", $first_day_timestamp)) ? $last - 7 : $last;
for($i = $start; $i < $last; $i++) {
    $timestamp  = mktime(0, 0, 0, $month, $i, $year);
    $color      = (date("m", $timestamp) == date("m", $first_day_timestamp)) ? '#000000' : '#ADADAD';
    $color      = (date("d.m.Y", $timestamp) == date("d.m.Y")) ? '#ff0000' : $color;
    $color      = (date("w", $timestamp) == 0 || date("w", $timestamp) == 6) ? '#fff000' : $color;
    $html      .= '<span style="color: '. $color .';">'. date("d", $timestamp) ."</span>\t";
    if($num % 7 == 0) {
        $html .= "\n";
    }
    $num++;
}
$html .= '</pre>';
echo $html;
{% endhighlight %}

<a id="limit-mysql"></a>
### Limited output in mysql (Pagination)

{% highlight php %}
<?php
$mysqli = new mysqli("localhost", "user", "password", "database")
$page = (isset($_GET['page'])) ? (int)$_GET['page'] : 1;
$limit = 10;
$offset  = abs(($page - 1)) * $limit;
$res = $mysqli->query("SELECT foo FROM bar LIMIT ". $offset .", ". $limit);
while($row = $res->fetch_array(MYSQLI_ASSOC)) {
    echo $row['foo'];
}

$res2 = $mysqli->query("SELECT COUNT(*) AS pagination_count FROM bar");
$row = $res2->fetch_array(MYSQLI_ASSOC);
$number_of_sites = ceil($row['pagination_count'] / $limit);
for($i = 1; $i <= $number_of_sites; $i++) {
    if($i == $page) {
        echo '<strong>[ '. $i .' ]</strong>';
    } else {
        echo '<a href="foo.php?page='. $i .'">'. $i .'</a>';
    }
}
{% endhighlight %}

<a id="tidy"></a>
### Formatted output with tidy

{% highlight php %}
<?php
$output = '<html> .... </html>';
$config = array('indent' => true,
                'indent-spaces' => 4,
                'wrap' => 200,
                'output-xhtml' => true);

$tidy = new tidy;
$tidy->parseString($output, $config);
$tidy->cleanRepair();
echo $tidy;
{% endhighlight %}
