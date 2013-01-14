---
layout: post
title: Why OAuth is awesome
---

So I finally got the authentication working on my Twitter application, and I've
decided that OAuth is actually pretty awesome. When it works, that is.

I had some initial troubles doing it though. Although there's quite a few
libraries out there for setting it up, there's a certain unavoidable amount of
difficulty involved. There's multiple redirects going on between your app and
the OAuth provider, and when you screw up, it's easy to run into the infamous
"infinite redirect" bug. 

It turns out my problem was the result of passing a relative URL (like
`/twitter/authorize`) to the API instead of an absolute one (such as
`http://mysupersecretapp.appspot.com/twitter/authorize`). The path
matched up to the Twitter API's one, so it ended up authorizing the
user, then redirecting to itself indefinitely.  You'd think they'd check for
that, but they don't.

Anyway, what I realized after setting it up is that for most applications, you
don't even really need to store the data you get back in a database! You can
just do what [yFrog](http://yfrog.com) (and others) do, and just perform a task
for the user after logging in. Then later on the login cookie expires, but who
cares? They can log in again later through Twitter...

Maybe I've just been missing out on something obvious here, but it seems like
this is a new and powerful way of building web applications. One where the app
is more like a plugin for one of the big sites, like Twitter or Facebook, and
doesn't have to store data for or manage its users in any way. If the whole
development process could be made just a little easier, it could really lower
the barrier for building small useful web applications by getting rid of the
user and data management overhead.

