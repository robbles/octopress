---
layout: post
title: "A simple solution for mobile web touch events"
date: 2012-08-19 22:40
comments: true
categories: mobile, javascript
---

I think anyone who's worked on building a "mobile-friendly" website has run into
the tricky problem of dealing with touch events. Let's face it, the default
behaviour for touching "clickable stuff" on a web page on all mobile platforms
is pretty nasty. You tap something, and 5 minutes later the page reacts.

When looking at
[touch events](http://www.w3.org/TR/touch-events/#the-touchstart---------event)
and how much more responsive they can make your page, there's a temptation to
just add them everywhere. After all, you get an almost native-like feel to
buttons and links when you make them respond to `ontouch`, instead of waiting
for the browser's `click` event to kick in. Unfortunately, this also has the
undesirable effect of messing up scrolling, resizing, and everything else you
can do with a multitouch device around these areas of the page. Ideally, we'd
like to be able to get the benefit of responding quickly to these touch events
without breaking the natural user experience of using the browser.

I've worked out a simple solution to this in the form of a minimal jQuery plugin
that gives you a replacement for `$.click()`, and binds both click events for
desktop platforms and uses a slightly more intelligent handling of the
`touchstart`, `touchmove`, and `touchend` to speed up touch event handling
without breaking the natural usage of the page. <!-- more -->To be honest, I'm still not sure
what the browsers do that takes so much longer to fire the click events, but
this results in a significantly faster experience on both MobileSafari and
Chrome for Android.

{% gist 3401532 %}

