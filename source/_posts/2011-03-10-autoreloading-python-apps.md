---
layout: post
title: Auto-reloading Python applications
---

I wrote a really handy little script for a project I'm working on involving a
[Sugar Activity](http://activities.sugarlabs.org//en-US/sugar/) that
automatically reloads the code whenever a file is changed. It borrows heavily
from the Django project's manage.py runserver command, and takes the form of an
activity launcher script.  You could use this for auto-reloading of any
single-threaded Python application, not just GUI applications.

<!-- more -->

Although the code monitoring part is basically the same as the Django server's,
I added an feature that's always seemed missing to me - When an
exception is thrown starting up the program, it prints out a stack trace, waits
10 seconds, and then resumes. This is really handy if you leave it running, and
are in the habit of compusively saving every 15 seconds, like me. Sometimes when
you make a syntax error in a core part of the application, the server crashes
and you have to restart it, which seems a bit silly. I also had to re-work it a
bit to play along with the GUI framework (PyGTK).

I started off with the [autoreload module used in
Django](http://code.djangoproject.com/browser/django/trunk/django/utils/autoreload.py),
which has a handy `python_reloader` wrapper function. If the application is
single threaded and doesn't use an event loop, you could probably just use this.
It works by starting a child process, which then starts two threads - one for
your application's entry point, and the other that monitors the code for changes
by inspecting `sys.modules` every second. If a change is detected, the monitor
thread shuts down the process. Meanwhile, the first process has been waiting for
this, and starts a brand new child process. This is all done through one script,
by cleverly setting an environment variable `RUN_MAIN` for the child process
which makes it start the two threads instead of its own child.

Unfortunately, I needed this to work inside of the GTK event loop, and not in
two different threads. There was an easy workaround though - the
`gtk.timeout_add` and `gobject.timeout_add` functions allow callbacks to be
scheduled at a predefined interval. I wrote a handler for calling the
`code_changed` function, and one for quitting the GTK main loop. `RESTART_CODE`
is a return code that's used to indicate the application ended because of a code
change, instead of just crashing.

{% codeblock lang:python %}

from autoreload import code_changed
import logging, gtk, subprocess

RESTART_CODE = 8
ERROR_SLEEP_TIME = 10

logging.basicConfig(level=logging.INFO)
log = logging.getLogger('activity_runner')

def exit():
    """ Quits the GTK event loop """
    log.warn('Quitting now')
    gtk.main_quit()


def watch_files():
    """ Watches imported files for changes """
    if code_changed():
        log.warn('Code was changed, exiting...')
        sys.exit(RESTART_CODE)
    else:
        return True
{% endcodeblock %}

Now I needed the main part of the activity runner, which sets up the options and
environment variables. I used `optparse` to add two command-line options, a
watch flag (which does what you'd expect), and a timeout flag. The timeout flag
exits the application after a given number of seconds, which is sometimes handy
for testing.

{% codeblock lang:python %}
import optparse

parser = optparse.OptionParser()

parser.add_option('-w', '--watch', action='store_true', 
        help="Restart activity when files are changed")

parser.add_option('-t', type='int', dest='timeout', action='store', 
        help="Stop activity after timeout", metavar='seconds')

options, args = parser.parse_args()

if options.timeout:
    log.info('Timeout after %d seconds' % options.timeout)
    gtk.timeout_add(1000 * options.timeout, exit)
{% endcodeblock %}

This next part is trickier. The first block should only execute in the child
process, but comes first so that it can disable starting a child process of its
own. Similar to `python_reloader` and friends, the next block turns on the
environment variable flag and runs itself as a child process. When the child
exits, the return code is used to decide what to do next. A zero means that the
event loop was exited normally, probably through the application GUI, so the
main loop ends when this happens. I changed the script here to allow for
errors, by adding a brief delay when a non-zero, non-restart code is returned,
instead of just quitting (the original behaviour)

{% codeblock lang:python %}
if os.environ.get("EXIT_ON_CHANGE") == "true":
    gtk.timeout_add(1000, watch_files)

    # Disable watching for sub-process
    options.watch = False

if options.watch:
    log.info('Watching code files...')

    while True:
        # Run again with env marker added
        env = os.environ.copy()
        env["EXIT_ON_CHANGE"] = 'true'
        retcode = subprocess.call(sys.argv, shell=False, close_fds=True, env=env)

        log.info('sugar-activity exited with code %d' % retcode)
        if retcode != RESTART_CODE:
            if not retcode:
                # If sugar-activity returns zero, just exit
                log.warn('Activity exited')
                sys.exit(0)
            else:
                # If sugar-activity returns an error code, restart after a delay
                log.error('Activity did not exit safely!')
                time.sleep(ERROR_SLEEP_TIME)
{% endcodeblock %}

Since the master process is stuck in that loop until the application exits, all
that's left now is to start the app! I'm leaving out some Sugar-specific
environment variable stuff, but this is where you'd call your application entry
point.

{% codeblock lang:python %}
from sugar.activity import main                                                 
main.main() 
{% endcodeblock %}

This might seem like a lot of effort just for an auto-reloading script, but in
my case it had a more important use. I'm now able to develop on my Mac while
sharing the code folders through NFS with a virtual machine I use to run the
application. The application just stays running on the VM all the time, and
whenever I save a code file, it restarts automatically. I set up the VM once,
display it on another monitor, and can leave it alone after that!

