---
layout: post
title: Mental Models of Software
comments: true
date: 2012-06-29
disqus_id: /blog/2012/08/24/mental-models-of-software/
---

I recently had an interesting experience when trying to buy food at a cafeteria
that made me think about how most people reason about computers. I tend to pay
for most things either with debit or credit cards, because I like how they track
my purchases automatically, and because I'm honestly just too lazy to make sure
I always have cash with me.

Anyone who's used an Interac machine knows how unreliable and finicky they can
be - although I'm sure there are difficult engineering and cost challenges
involved in designing them, I think they could do a lot better than the
currently available systems and their UI's. Usually when something goes wrong,
you can try the transaction again, or at least just pay with a different method.
However, this time, the machine completely locked up, and then somehow froze the
cash register as well.

The interesting part of what happened next had nothing to do with the payment
machines themselves, but how the cashier responded to this. <!-- more -->She began to push
random buttons on the machine, and remove and re-insert the card repeatedly.
When this had no effect after several minutes, I offered to pay with cash that I
borrowed from a friend who was luckily ahead of me in line. When she discovered
that the cash register interface had failed as well, she responded the same way - 
blindly pushing buttons and waiting for something to change. At this point, a
long line was forming and a co-worker wandered over to help. He suggested to
restart the machine, mentioning "it usually helps, but it takes forever to start
up again". She gave this a try, and a couple of minutes later the machine was up
and working again.

So what's the point of this totally mundane story? I realized something
interesting about the behavior of the cashier, and about people who don't have
significant technical experience (e.g. programming, engineering) when they try
to troubleshoot software problems.

A software developer would see this problem and immediately go through a thought
process like this:

- The payment machine has run into some sort of software bug
- It is a computer system that most likely has (a) permanent storage like
flash or a hard drive and (b) non-permanent state stored in memory that
affects the running of the software and (c) a network interface that
receives data from an external service
- The buggy state is probably not permanent, and probably results from the
state of the software being messed up, perhaps from unexpected input from
the UI or the network
- Therefore, restarting the device should provide it with a "clean slate"
that allows it to go back to a known working state and should be the easiest
way to fix the problem

A non-technical person might think about it like this:

- The machine is broken, either because of something I did wrong, or because
of something wrong with the card
- I know how to use it when it's not broken, but not when it's in this
incorrect state
- The only effect I can have on this machine is through interacting with it
in a similar way that I did when it was working
- If I restart the machine, it will still be broken

The question is, why didn't the cashier think of restarting the device as a
solution right away, instead of trying to keep interacting with a device
that was obviously not responding? I think this is due to a flawed mental model
of how the machine works. 

A computer is somewhat unique among tools in that it has temporary memory. In
comparison, a power drill doesn't have any memory, and expecting it to behave
differently when you unplug it and plug it back in again would be silly.
However, I think most people's mental model of how software works IS based on
the same mental model that they have for tools and simple appliances. Any change
to a physical tool's behavior (such as stripping a drill bit, or burning out the
motor) is a physical one, and thus you can expect it to remain through a power
outage. Many software bugs, on the other hand, are ephemeral and won't persist
through a restart of the machine.

So what does this mean for people who design software? It means that you can't
assume the user of an application you design shares your mental model of how the
application works, or even of the computer system that it runs atop. The way the
user uses the application, and how errors are handled or presented to the user,
should be based on metaphors for real-world things that they're used to and
understand intuitively. 

One example of this would be an mobile app that keeps a record of its state so
that it can seamlessly restore back to the same state when the app is closed and
reopened. This gives the illusion of an interface that is persistent -
the user left it a certain way, and when they came back, it was still like that,
just like a physical object. As an application developer, it's obvious that the
app doesn't REALLY work this way, but that doesn't mean we should avoid carefully
constructing this illusion for the user. Otherwise they likely won't be able to
reason about how to fix a problem, since they have a very limited concept of how
mobile apps are started/restarted and when they stay running.
