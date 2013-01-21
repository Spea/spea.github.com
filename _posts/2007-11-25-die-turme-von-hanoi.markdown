---
layout: !binary |-
  cG9zdA==
status: !binary |-
  cHVibGlzaA==
published: true
title: Die T&uuml;rme von Hanoi
author: !binary |-
  U3BlYQ==
author_login: !binary |-
  YWRtaW4=
author_email: !binary |-
  Y2VvbjIwMDBAZnJlZW5ldC5kZQ==
author_url: !binary |-
  aHR0cDovL3NwZWFuZXQuaW5mbw==
wordpress_id: 21
wordpress_url: !binary |-
  aHR0cDovL3NwZWFuZXQuaW5mby8yMDA3LzExLzI1L2RpZS10dXJtZS12b24t
  aGFub2kv
date: 2007-11-25 01:25:10.000000000 +01:00
categories:
- PHP
tags: []
comments: []
---
Was bittesch&ouml;n sind die T&uuml;rme von Hanoi? Nunja, das ist eigentlich schnell erkl&auml;rt. Bei den T&uuml;rmen von Hanoi handelt es sich um 3 T&uuml;rme, wobei auf einen dieser T&uuml;rme 64 Goldscheiben liegen, diese 64 Scheiben sollen also auf einen der anderen 2 T&uuml;rme verschoben werden. Die eigentliche Geschichte handelt von einem M&ouml;nch, dem wenn es gelingen w&uuml;rde alle 64 Scheiben zu verschieben, dass dann das Ende der Welte gekommen w&auml;re.
Dabei gelten folgende Regeln:
<ul>
	<li>Die Scheiben m&uuml;ssen auf eine der anderen 2 T&uuml;rme abgelegt werden</li>
	<li>Es darf immer nur eine Scheibe bewegt werden</li>
	<li>Es darf niemals eine gr&ouml;&szlig;ere Scheibe auf eine kleinere abgelegt werden</li>
</ul>

Die Frage ist nun, wie setzt man das so um, dass man es in einer Programmiersprache verwenden kann.
Dazu wurde ein rekursiver Algorithmus gefunden, der wie folgt aussieht:
<pre language="text">funktion bewege (Zahl i, Stab a, Stab b, Stab c) {
    falls (i > 0) {
       bewege(i-1, a, c, b);
       verschiebe oberste Scheibe von a nach c;
       bewege(i-1, b, a, c);
    }
}</pre>

In anbetracht der Komplexit&auml;t und Aufwendigkei 64 Scheiben nach diesen Regeln auf einen anderen Turm zu verschieben, ist die Funktion sehr sehr einfach (was man vielleicht nicht erwartet h&auml;tte).

Ich habe vor etwas l&auml;ngerer Zeit ein PHP-Script dazu geschrieben, welches auch noch die einzelnen Verschiebungen anzeigt (also Scheibe $x nach Turm $y verschieben). Das Script ist aber auf 12 Scheiben beschr&auml;nkt, da es ansonsten  zuviel Speicher des Browsers kosten w&uuml;rde.

Das Script ist <a href="http://speanet.info/examples/hanoi.php">hier</a> zu finden.

Der Code dazu folgt in K&uuml;rze
