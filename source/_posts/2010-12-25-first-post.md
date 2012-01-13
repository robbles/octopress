---
layout: post
title: My First Post
---

It's Christmas Day, and I'm building a blog. Because I can.

Luckily, it hasn't taken me very long. I decided to check out
[Jekyll](http://github.com/mojombo/jekyll) by starting with a fork of its
[creator's site](http://github.com/mojombo/mojombo.github.com).

Now that I have it working, I just need to come up with a fantastic new design
of my own, point some domain names at this and I'm set. Might even port my
portfolio-website-in-progess over to Jekyll and Liquid as well.  

Porting of old posts from my other attempts at blogging might happen at some
point in the distant future, when I'm very bored. Definitely not tonight.
Here's a random piece of Python code to show how awesome Pygments support is:

{% highlight python %}

def circle(size, fore, back):
    for y in range(size):
        for x in range(size):
            xi, yi = round(x - size/2 + 0.5), round(y - size/2 + 0.5)
            radius = abs((xi**2 + yi**2) - (size/2)**2)
            print fore if radius < (size/2) else back,
        print

circle(20, 'x', ' ')

{% endhighlight %}

