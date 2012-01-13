---
layout: post
title: Decorating With Processing.js
---

I finally got around to porting [the script that powers the title
animation](/sketches/processingjstitle.html) to Javascript. Why would I
want to do this? Two reasons:

- To adaptively adjust the framerate based on the user's browser
- To make it respond interactively to browser events other than on the canvas

One problem I've run into with Processing.js is that
the code ends up encapsulated in its own little "Java syntax bubble", and as far
as I know, the outside page can't access it at all. I rewrote it based on the
example given on the Processing.js site ([Writing Processing Code with
Javascript](http://processingjs.org/learning)), which made it simple to
add the next two changes.

The performance of HTML canvas on iOS (and probably other mobile OSs as well) is
pretty poor, so I thought it would be a good idea to drop the framerate a bit. A
simple browser check (using a jQuery plugin) is enough to do this.


Since the animation looks a lot cooler in fast motion, I decided to make it so
it speeds up to 3X the frame rate when a visitor mouses over the title. Since
I'm using jQuery for everything else on this site, it was easy enough to bind to
the event listener and toggle back and forth between the two. At first I thought
that the frameRate function would only work inside setup(), but it was not so.
It looks like it's possible to change the speed at any time from any event in
the page. 

This is most of what I added:
{% highlight javascript %}

var mobile = false;
try {
    mobile = jQuery.browser.mobile;
} catch(err) { }

var sketch = function(p) {  
    var num_elements = 80;
    p.size(720,100);

    // Wimp out for mobile browsers
    p.frameRate(mobile? 5 : 10);

    // Speed up 3X on mouseenter
    $(canvas).parent().mouseenter(function() {
        p.frameRate(30);
    })
    .mouseleave(function() {
        p.frameRate(10);
    });
    ...
}

{% endhighlight %}

The [full javascript version](https://gist.github.com/864953) is on GitHub.
I think it's a pretty simple example of how to convert a Processing sketch to
integrate it with your page.

