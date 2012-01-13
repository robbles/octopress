---
layout: post
title: Using rlwrap with the Node.js REPL
---

I've recently been working on a couple of small projects using
[Node.js](http://nodejs.org/), and I'm loving it's simplicity and speed so far.
One thing I have been missing though, thanks to my experience with Python and
IPython, is the lack of a sane REPL for prototyping and testing out bits of
code. I know, you can just run `node` and type stuff in, but the way that
interpreter behaves is far from what I'd call "sane". There's no history, and if
you screw up the order of brackets, or get lost in a multiline expression, you
get to experience this wonderful prompt:

{% highlight javascript %}
> var x = function() {

[object Context]:1


... 
{% endhighlight %}

Fortunately, the node.js docs suggest an alternative, using the handy
[rlwrap](http://utopia.knoware.nl/~hlub/rlwrap/rlwrap.html) tool, which wraps
any command inside a readline shell and gives you about a million options. I
extended the provided example a bit, and ended up with this:

{% highlight bash %}
alias node='env NODE_NO_READLINE=1 rlwrap -p Green -S "node >>> " node'
{% endhighlight %}

This tells node not to use it's built-in readline functionality, replaces the
prompt with a custom colored one, and automatically starts maintaining a history
file for this command at `~/.node_history`. Pretty fantastic. Although rlwrap
does have some other options for messing around with alternate prompts, it looks
like the simplest way of getting around node's multiline REPL issues is to just
use the built-in `.break` command. This seems to fix it in most cases.
    




