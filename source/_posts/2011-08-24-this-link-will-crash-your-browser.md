---
layout: post
title: This link will crash your browser
comments: true
---

<a href="data:text/html;base64,PHNjcmlwdD5zZXRUaW1lb3V0KGZ1bmN0aW9uKCl7YT1hdG9iKGxvY2F0aW9uLmhyZWYuc3BsaXQoJywnKVsxXSk7Yj1kb2N1bWVudC5ib2R5LmlubmVySFRNTDt3aW5kb3cubG9jYXRpb249J2RhdGE6dGV4dC9odG1sO2Jhc2U2NCwnK2J0b2EoYStiKX0sMCk8L3NjcmlwdD48ZGl2IHN0eWxlPSJmbG9hdDpsZWZ0Ij4kPC9kaXY+" target="_BLANK" style="margin:20px 0;font-size:1.2em; text-decoration:underline">
Here it is. Go ahead and click on it, we both know you can't resist.
</a>

If you look closely at the location that link's pointing to, you'll notice it's
not a website. It's actually a [Data URI](http://en.wikipedia.org/wiki/Data_URI_scheme), a special kind of address
that allows you to embed data for a browser directly into the link itself. 

Data URIs begin with `data:` and a mimetype to tell the browser how to interpret
the content. After that comes an optional directive `;base64`, which means that
the following content is encoded as a [Base64](http://en.wikipedia.org/wiki/Base64#HTML) string. In this case, the
content is encoded to make it easier to process the URL from inside the page.
The content then follows after a comma.

<!-- more -->

Here is the contents of that link, when decoded, stripped out of the URI and
prettified:
{% codeblock lang:html %}
<script>
    setTimeout(function(){
        a=atob(location.href.split(',')[1]);
        b=document.body.innerHTML;
        window.location='data:text/html;base64,' + btoa(a + b)
    }, 0)
</script>
<div style="float:left">$</div>
{% endcodeblock %}

## How does it work?

Because the contents of window.location are encoded in Base64, it's a relatively
simple task to parse the entire page out of it by splitting on the single comma.
We can then grab just the page content with the `innerHTML` property of the
body tag. Adding these two together and reencoding into a new Data URI yields
the exact same page, *but with the content doubled*. 

We then simply redirect the browser to our newly created "page". The content in
this case is a simple div tag with a dollar sign, which causes the browser
window to fill up with these symbols before crashing or freezing.

The key trick here is that the browser will load the new page almost instantly,
and the content is being produced entirely locally, without having to wait for a
server and read data with HTTP. I'm actually not sure what crashes each browser,
but I'm guessing it's that none of them place limits on the amount of content,
which in this case is growing exponentially.

## Does it work on all browsers?

Actually, no. Ironically enough, it doesn't affect Internet Explorer. Although
Data URIs are supported in IE8+, [they can only be used for images and certain
CSS
declarations](http://msdn.microsoft.com/en-us/library/cc848897.aspx). Also,
because of its one-process-per-tab model, Google Chrome isn't affected too much
by this; it just gives you the unhappy file folder.


## Is this dangerous?

I don't think so. At least, I can't think of anything useful to do with it other
than posting a link somewhere to annoy people. Also, most websites should flag a
Data URI as an invalid link, preventing you from posting it to a public area.

