---
layout: !binary |-
  cG9zdA==
status: !binary |-
  cHVibGlzaA==
published: true
title: Filtering a huge list of elements with jQuery
author: !binary |-
  U3BlYQ==
author_login: !binary |-
  YWRtaW4=
author_email: !binary |-
  Y2VvbjIwMDBAZnJlZW5ldC5kZQ==
author_url: !binary |-
  aHR0cDovL3NwZWFuZXQuaW5mbw==
wordpress_id: 255
wordpress_url: !binary |-
  aHR0cDovL3NwZWFuZXQuaW5mby8/cD0yNTU=
date: 2012-09-25 17:45:25.000000000 +02:00
categories:
- Javascript
tags: []
comments: []
---
I wanted to filter a huge list of cities (~1800 entries) with jQuery. So I added an input field which triggers a filter event. The event basically looked like this

{% highlight javascript %}
var input = $("input");
if (input.val().length === 0) {
  $("#cities li").show();
} else {
  $("#cities li:contains('" + (input.val()) + "')").show();
  $("#cities li:not(:contains('" + (input.val()) + "'))").hide();
}
{% endhighlight %}

Unfortunately the whole site was blocked for ~3 seconds when the show method had to process a lot of elements (> 500).

At first I tried to put the whole event into a setTimeout function, so it would no longer block the script execution. Sadly this hasn't changed anything, the whole site was still blocked for several seconds. Profiling the script made it clear, that it was in fact kinda fast (50 - 150ms).

After doing some research on this, I found out that the problem is a repaint/reflow caused by adding or removing <strong>display: none</strong> to the elements.

To avoid a repaint/reflow I tried "hiding" the elements from the user by moving them to a place where they can't see them.

{% highlight javascript %}
var input = $("input");
if (input.val().length === 0) {
  $("#cities li").css("position", "").css("top", "");
} else {
  $("#cities li:contains('" + (input.val()) + "')").css("position", "").css("top", "");
  $("#cities li:not(:contains('" + (input.val()) + "'))").css("position", "absolute").css("top", "-20000em");
}
{% endhighlight %}


As it turns out the site is no longer blocked for several seconds, so the problem really was the repaint/reflow.

There are of course some other methods to avoid a repaint/reflow. For example copying the complete dom-list, make all necessary changes and replace the original with the copied/changed one. This way there is only one overall reflow and no longer one for every element which will be hidden/displayed.

If you want to read more about repaint/reflow in browsers, take a look at <a href="http://www.phpied.com/rendering-repaint-reflowrelayout-restyle/">this article</a>
