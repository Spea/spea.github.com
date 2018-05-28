---
layout: !binary |-
  cG9zdA==
status: !binary |-
  cHVibGlzaA==
published: true
title: Hohe Auslastung des Interrupt-Prozess unter Windows
author: !binary |-
  U3BlYQ==
author_login: !binary |-
  YWRtaW4=
author_email: !binary |-
  Y2VvbjIwMDBAZnJlZW5ldC5kZQ==
author_url: !binary |-
  aHR0cDovL3NwZWFuZXQuaW5mbw==
wordpress_id: 25
wordpress_url: !binary |-
  aHR0cDovL3NwZWFuZXQuaW5mby8yMDA4LzAxLzI2L2hvaGUtYXVzbGFzdHVu
  Zy1kZXMtaW50ZXJydXB0LXByb3plc3MtdW50ZXItd2luZG93cy8=
date: 2008-01-26 17:52:16.000000000 +01:00
categories:
- Common
- Findings
tags: []
comments:
- id: 29
  author: Martin
  author_email: !binary |-
    c3BhbTAwMUBnbXguZGU=
  author_url: !binary ""
  date: !binary |-
    MjAwOC0wNi0xOCAxNjoyOTo1NiArMDIwMA==
  date_gmt: !binary |-
    MjAwOC0wNi0xOCAxNToyOTo1NiArMDIwMA==
  content: ! "Habe das gleiche Problem, \r\n\r\nes trat auf nach dem ich eine Grafikkarte
    austauschen m&uuml;&szlig;te da die alte vergl&uuml;ht ist. Die angegebene L&ouml;sung
    hat bei mir leider nicht funktioniert.\r\nVorher war nur ein CPU Kern betroffen,
    jetzt sind es beide ;o)\r\n\r\nnaja... irgendeine L&ouml;sung werd ich schon finden\r\n\r\nMartin"
- id: 81
  author: Felix
  author_email: !binary |-
    d293X21vcm11c0Bob3RtYWlsLmRl
  author_url: !binary ""
  date: !binary |-
    MjAwOS0wNC0yNSAyMzowOTozMSArMDIwMA==
  date_gmt: !binary |-
    MjAwOS0wNC0yNSAyMjowOTozMSArMDIwMA==
  content: ! "Ich bin soooo dankbar xD\r\nhatte das Problem nach dem neu-aufsetzten
    von Windowns... hab mich dann gefragt warum alles immer hakte, stockte und die
    Maus ganz komisch &uuml;er den Desktop h&uuml;pfte. Hab Tage gebraucht um rauszufinden,
    wer die Leistung &uuml;berhaupt inanspruch nahm. Stand schlie&szlig;lich nichts
    im Task-Manneger. Hab mir dann \"process explorer\" und da stands dann. Ab da
    wars dann einfach, DANK DIESEM Tread"
- id: 83
  author: Klaus
  author_email: !binary |-
    YmVhY2hlckB3aWxsLWhpZXItd2VnLmRl
  author_url: !binary ""
  date: !binary |-
    MjAwOS0wNS0xMSAxMzoyMDoxMyArMDIwMA==
  date_gmt: !binary |-
    MjAwOS0wNS0xMSAxMjoyMDoxMyArMDIwMA==
  content: Habe das selbe Problem auf dem beschriebenen Weg beseitigen k&ouml;nnen.
    Danke! Vielleicht sollte noch ausdr&uuml;cklich erw&auml;hnt werden, dass man
    bei XP keinerlei (Installations-)CDs f&uuml;r die Aktion ben&ouml;tigt - die Ger&auml;te
    werden automatisch erkannt und neu installiert.
- id: 4657
  author: ''
  author_email: !binary ""
  author_url: !binary |-
    aHR0cDovL3d3dy5tb2Rlcm5ib2FyZC5kZS9zb2Z0d2FyZS1wcm9ibGVtZS8x
    MDMyMjEtcGMtaGFlbmd0LXJ1Y2tlbHQtYWxsZS01LTIwLW1pbi1mdWVyLWNh
    LTEtc2VrdW5kZS5odG1sI3Bvc3Q4NjYwMDU=
  date: !binary |-
    MjAxMS0wNy0wNiAwOTo0NDo1OCArMDIwMA==
  date_gmt: !binary |-
    MjAxMS0wNy0wNiAwODo0NDo1OCArMDIwMA==
  content: ! '[...]  [...]'
---
Vor kurzem hatte ich ein eher merkw&uuml;rdiges Problem. Ab und zu, ohne feststellen zu k&ouml;nnen wie und wann das auftrat, hatte ich durchgehend ~50% CPU-Auslastung. Die Prozessliste im Taskmanager hat aber keinerlei Hinweis darauf gegeben, welcher Prozess den CPU so auslastet.

Nach ein bisschen rumfragen wurde mir gesagt ich sollte doch mal die <a href="http://www.microsoft.com/germany/technet/sysinternals/default.mspx">sysinternals</a> von Windows benutzen, da dort ein Prozessmanager dabei ist, der auch versteckte Prozesse anzeigt. Da es auch m&ouml;glich ist Prozesse mit dem Programm <em>hidden.exe</em> versteckt starten zu k&ouml;nnen, dachte ich es handelt sich um ein Rootkit oder einen Virus der diese CPU-Auslast verursacht. Als ich dann das Programm gestartet habe, war ich doch erstmal recht verwundert, hier erstmal ein Screenshot davon.

<img src="http://speanet.info/bilder/process2.jpg" alt="Prozessauslastung" />

Wie man hier gut sehen kann, braucht ein sogenannter Interrupts-Prozess 40% CPU-Auslastung, und ein DPCs-Prozess 10% Auslastung. Was Interrupts sind, und wof&uuml;r die gut sind, kann <a href="http://de.wikipedia.org/wiki/Interrupt">hier</a> nachgelesen werden.

Als n&auml;chstes war jetzt die Frage, warum braucht dieser Prozess soviel CPU?
Eine Antwort auf diese Frage habe ich leider nie wirklich gefunden, aber ich habe eine L&ouml;sung zu dem Problemm gefunden, das ebenso einfach wie trivial ist.

In einem Forum stand, dass man im Ger&auml;temanager unter <em>IDE ATA/ATAPI-Controller</em> alle Unterpunkte deinstallieren soll, dann Windows neustarten und es sollte wieder funktionieren.
Gesagt getan, und nachdem die ganzen Treiber f&uuml;r die Controller und Kan&auml;le wieder installiert wurden, lief alles wieder so sch&ouml;n wie vorher. 
Ich frag mich zwar immer noch, warum man es gerade so machen musste, aber da wieder alles so wie vorher l&auml;uft, ist mir das eigentlich herzlichst egal.

Vielleicht hat ja mal jemand das gleiche Problem und ich konnte damit helfen.
