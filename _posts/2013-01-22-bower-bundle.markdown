---
layout: post
title: Handle bower dependencies in symfony2 with SpBowerBundle
category : Coding
tags : [symfony2, bundle, bower, SpBowerBundle]
year: 2013
month: 1
day: 22
---

## SpBowerbundle

In this post I will talk about [SpBowerBundle](https://github.com/Spea/SpBowerBundle) which I created a few month ago.
Some of the benefits you get is a new command to install your defined bower dependencies and an automatic registration
of all assets in the assetic manager (if needed). You can even add specific assetic filters to your bower packages.

<div class="alert alert-block alert-info">
    <i class="icon-info-sign icon-large"></i>  I used the <a href="https://github.com/symfony/symfony-standard">symfony-standard</a> edition in this article.
</div>
<hr />

### First Step

At first we are going to install the bundle with composer

<pre class="prettyprint lang-sh">
$ php composer.phar require sp/bower-bundle
Please provide a version constraint for the sp/bower-bundle requirement: dev-master
composer.json has been updated
Loading composer repositories with package information
Updating dependencies
  - Installing sp/bower-bundle (dev-master 9c6ce13)
    Cloning 9c6ce13d3b802fd58a095aff9cc93a59d873595f

Writing lock file
Generating autoload files
Clearing the cache for the dev environment with debug true
Installing assets using the hard copy option
Installing assets for Symfony\Bundle\FrameworkBundle into web/bundles/framework
Installing assets for Acme\DemoBundle into web/bundles/acmedemo
Installing assets for Symfony\Bundle\WebProfilerBundle into web/bundles/webprofiler
Installing assets for Sensio\Bundle\DistributionBundle into web/bundles/sensiodistribution
</pre>

Make sure to enable the bundle in your AppKernel.php

<pre class="prettyprint lang-php">
// ...
    public function registerBundles()
    {
        $bundles = array(
            // ...
            new Sp\BowerBundle\SpBowerBundle(),
        );
    }
// ...
</pre>
<hr />

### Configuration

Now that we installed and activated the bundle we can go on with the configuration.

<pre class="prettyprint linenums">
# app/config/config.yml
sp_bower:
    bundles:
        AcmeDemoBundle: ~
</pre>

This tells the bundle to activate bower handling with the default values for the AcmeDemoBundle.
The default lookup path for the bower dependency file ```component.json``` is ```$bundle/Resources/config/bower```.
<hr />

### Adding some bower dependencies.

The AcmeDemoBundle is activated for bower handling, lets add some dependencies to it! To do this, we create a file called
```component.json``` in the directory ```src/Acme/DemoBundle/Resources/config/bower/```.

<pre class="prettyprint linenums lang-json">
{
    "dependencies": {
        "bootstrap": "latest"
    }
}

</pre>
<hr />

### Install bower dependencies

Everything is set up correctly, so we are now able to install the defined dependencies with the integrated
command. By default the dependencies will be installed in the directory ```$bundle/Resources/public/components```.

<pre class="prettyprint lang-sh">
$ app/console sp:bower:install
Installing bower dependencies for "AcmeDemoBundle" into "/home/spea/workspace/test-bower-bundle/src/Acme/DemoBundle/Resources/config/bower/../../public/components"
bower cloning git://github.com/twitter/bootstrap.git
bower cached git://github.com/twitter/bootstrap.git
bower fetching bootstrap
bower checking out bootstrap#v2.2.2
bower copying /home/spea/.bower/cache/bootstrap/c1ee45ee19795a66de4a0c45758fe0b1
bower cloning git://github.com/components/jquery.git
bower cached git://github.com/components/jquery.git
bower fetching jquery
bower checking out jquery#1.8.3
bower copying /home/spea/.bower/cache/jquery/cf68c4c4e7507c8d20fee7b5f26709d9
bower installing bootstrap#2.2.2
bower installing jquery#1.8.3
</pre>
<hr/>

### Use the automatically registered assets

Till now there is nothing special, the bundle installs bower dependencies which could also be achieved with the command
```bower install```, but a great benefit of this bundle is the automatic registration of all your dependencies
in the assetic manager.

So if you now want to use the installed bootstrap assets, you can do so by simply writing the following.

{% capture assetic_usage %}
{% raw %}{% javascripts
    "@bootstrap_js"
%}
    <script src="{{ asset_url }}"></script>
{% endjavascripts %}
{% stylesheets
    "@bootstrap_css"
%}
    <link rel="stylesheet" href="{{ asset_url }}" />
{% endstylesheets %}
{% endraw %}
{% endcapture %}
<pre class="prettyprint">{{ assetic_usage|xml_escape }}</pre>

Since ```bootstrap``` has a dependency on ```jquery``` it will be automatically included in the generated assets.

<div class="alert alert-block alert-warning">
    <i class="icon-warning-sign icon-large"></i> Some dependencies (like <a href="https://github.com/xing/wysihtml5">wysihtml5</a>)
    will not be automatically registered in the assetic manager because some of them haven't defined a <strong>main</strong>
    section in their <strong>component.json</strong>. But you can still add them manually.
</div>

But wait, there is more! You can also add assetic filters to all (or only some specific) packages.

Let's say that you want to add the ```uglifyjs``` fillter to all javascript packages and the ```cssrewrite``` filter
only to the bootstrap package, then you would write the following:

<pre class="prettyprint linenums">
# app/config/config.yml
sp_bower:
    bundles:
        AcmeDemoBundle: ~
    assetic:
        enabled: true
        filters:
            js:
                - uglifyjs
            packages:
                bootstrap:
                    css:
                        - cssrewrite

</pre>
<hr />

### Advanced Configuration

#### Adjust asset and bower dependency directory.

Sometimes you want to use a different asset or config directory or you even want to name your bower dependency file differently.

So lets assume you want to name your dependency file ```bower-dependencies.json``` and you want to place this file in the
following directory: ```%kernel.root_dir%/Resources/bower```.

<pre class="prettyprint linenums">
# app/config/config.yml
sp_bower:
    bundles:
        AcmeDemoBundle:
            config_dir: %kernel.root_dir%/Resources/bower
            json_file: bower-dependencies.json
</pre>

After we renamed and moved the configuration file to the desired directory we can install the dependencies

<pre class="prettyprint lang-sh">
$ app/console sp:bower:install
Installing bower dependencies for "AcmeDemoBundle" into "/home/spea/workspace/test-bower-bundle/app/Resources/bower/../../public/components"
bower cloning git://github.com/twitter/bootstrap.git
bower cached git://github.com/twitter/bootstrap.git
bower fetching bootstrap
bower checking out bootstrap#v2.2.2
bower copying /home/spea/.bower/cache/bootstrap/c1ee45ee19795a66de4a0c45758fe0b1
bower cloning git://github.com/components/jquery.git
bower cached git://github.com/components/jquery.git
bower fetching jquery
bower checking out jquery#1.8.3
bower copying /home/spea/.bower/cache/jquery/cf68c4c4e7507c8d20fee7b5f26709d9
bower installing bootstrap#2.2.2
bower installing jquery#1.8.3
</pre>

As you can see, the assets are now installed into ```%kernel.root_dir%/public/components```, this is due to the
fact that the default asset path is always relative to the config directory, so it is necesary to adjust the asset path
too.

<pre class="prettyprint linenums">
# app/config/config.yml
sp_bower:
    bundles:
        AcmeDemoBundle:
            config_dir: %kernel.root_dir%/Resources/bower
            asset_dir: %kernel.root_dir%/Resources/public/acme_bundle_components
            json_file: bower-dependencies.json
</pre>

Lets run the install command again

<pre class="prettyprint lang-sh">
$ app/console sp:bower
Installing bower dependencies for "AcmeDemoBundle" into "/home/spea/workspace/test-bower-bundle/app/Resources/public/acme_bundle_components"
bower cloning git://github.com/twitter/bootstrap.git
bower cached git://github.com/twitter/bootstrap.git
bower fetching bootstrap
bower checking out bootstrap#v2.2.2
bower copying /home/spea/.bower/cache/bootstrap/c1ee45ee19795a66de4a0c45758fe0b1
bower cloning git://github.com/components/jquery.git
bower cached git://github.com/components/jquery.git
bower fetching jquery
bower checking out jquery#1.8.3
bower copying /home/spea/.bower/cache/jquery/cf68c4c4e7507c8d20fee7b5f26709d9
bower installing bootstrap#2.2.2
bower installing jquery#1.8.3
</pre>

<div class="alert alert-block alert-info">
    <i class="icon-info-sign icon-large"></i> It is also possible to use the bundle notation (@AcmeDemoBundle/path/to/directory) for the <strong>config_dir</strong>
    and <strong>asset_dir</strong> options.
</div>

#### Install on warmup

If you want to install the bower dependencies on every cache warmup, you have to enable the option
```install_on_warmup```.

<pre class="prettyprint linenums">
# app/config/config.yml
sp_bower:
    install_on_warmup: true
</pre>

#### Kepping the generated bower configuration file

The bundle automatically creates a ```.bowerrc``` file based on the configuration you made in the ```app/config/config.yml``` file when
installing the dependencies. The default setting is, that this file is only created temporarily, but sometimes you might
want to keep this file. By setting the option ```keep_bowerrc``` to ```true``` the generated files will be placed into the same
directory where you have your ```component.json```.

<pre class="prettyprint linenums">
# app/config/config.yml
sp_bower:
    keep_bowerrc: true
</pre>

#### Disable automatic assetic registration of used bower packages

If, for whatever reason, you don't want this bundle to automatically register the packages in the assetic
manager, you must set the ```assetic``` option to false.

<pre class="prettyprint linenums">
# app/config/config.yml
sp_bower:
    assetic: false
</pre>
<hr />

#### Use the provided composer script to install dependencies on composer install/update

This bundle provides a composer script to install the dependencies when ```composer update/install``` is executed.

<pre class="prettyprint lang-json linenums">
{
   "scripts": {
       "post-install-cmd": [
           "Sp\\BowerBundle\\Composer\\ScriptHandler::bowerInstall"
       ],
       "post-update-cmd": [
           "Sp\\BowerBundle\\Composer\\ScriptHandler::bowerInstall"
       ]
   }
}
</pre>
<hr />

I hope this blog post helped you to easily learn and integrate the SpBowerBundle. I'm always open for criticism
(positive and negative), so don't hesitate to share your thoughts in a comment.
