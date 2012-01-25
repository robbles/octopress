---
layout: post
title: Deploying Django on Google App Engine
---

Today I spent an embarrassingly long time trying to figure out the "nicest" way
to set up my Django project to work with Google App Engine. The main problem is
that the development server mangles the Python package import system in some
mysterious way to emulate the environment of App Engine itself. This makes using
[virtualenv](http://pypi.python.org/pypi/virtualenv) pretty much impossible, at
least from what I've seen so far. The best I was able to pull off was running an
interactive shell that could access the GAE libraries. This is kind of useless
without being able to run the server though. What I wanted was to have a
workflow like the following, where I could extend the `deploy` command to
bundle the installed packages from the virtualenv automatically:

{% codeblock lang:bash %}

rob@macpro: ~/ $ workon appengine
(appengine)rob@macpro: ~/ $ cdvirtualenv myapp/
(appengine)rob@macpro: myapp/ $ pip install django-awesome-app another-lib
...
(appengine)rob@macpro: myapp/ $ ./manage.py runserver
...
(appengine)rob@macpro: myapp/ $ ./manage.py deploy
...

{% endcodeblock %}

Luckily, it turns out the [django-nonrel and
djangoappengine](http://www.allbuttonspressed.com/projects/djangoappengine)
projects have solved most of the problems with this, as long as you do things
their way. I first tried to follow my own "modified" version of their setup
tutorial where I bundled all of the libraries into zip files to be used with
zipimport. Don't do this; It doesn't work. The djangoappengine setup code
actually assumes it's located in a top-level folder in your project, and
hardcodes the path to the app.yaml configuration file accordingly. More
importantly, a lot of Django apps won't work from inside a zipfile, probably
because of the way templates and static files are loaded. 

Anyways, it's working quite well now. There's still some weird things missing
in the django-nonrel project, like being able to reliably use the dumpdata or
remote commands, but nothing that can't be worked around. More soon on actually
building a working site with Django on GAE. Previously I've only used it for
miniature experiments, like [turk-keypad](http://turk-keypad.appspot.com).




