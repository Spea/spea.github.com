---
layout: !binary |-
  cG9zdA==
status: !binary |-
  cHVibGlzaA==
published: true
title: Die IF-Schleife
author: !binary |-
  U3BlYQ==
author_login: !binary |-
  YWRtaW4=
author_email: !binary |-
  Y2VvbjIwMDBAZnJlZW5ldC5kZQ==
author_url: !binary |-
  aHR0cDovL3NwZWFuZXQuaW5mbw==
wordpress_id: 36
wordpress_url: !binary |-
  aHR0cDovL3NwZWFuZXQuaW5mby8/cD0zNg==
date: 2008-12-04 17:16:59.000000000 +01:00
categories:
- Common
tags: []
comments: []
---
Ja, richtig gelesen, es handelt sich hierbei um die mysteri&ouml;se<em> IF-Schleife</em>.
Nat&uuml;rlich ist dieser Ausdruck Schwachsinn aber ich hab mich mal ran gemacht, und versucht eine IF-Schleife zu programmieren, und das ist was dabei rauskam.

{% capture if_loop %}
<?php
$c = (isset($c)) ? $c + 1 : 0;
if ($c != 10) {
    echo "foo";
    include __FILE__;
}
{% endcapture %}
<pre class="prettyprint lang-php linenums">{{ if_loop|xml_escape }}</pre>

Wobei sich nat&uuml;rlich die Bedingung f&uuml;r den Abbruch der <em>IF-Schleife</em> variieren l&auml;sst, man muss nur darauf achten, dass man die Variable nicht &uuml;berschreibt.
Und bitte <strong>bitte</strong> benutzt das nicht f&uuml;r den Produktiveinsatz.
