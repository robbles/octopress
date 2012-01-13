---
layout: post
title: Twelve Days of Christmas
---

I wrote a program to print out the verses of the Twelve Days of Christmas. This
started from an argument with my sister about whether the gifts are cumulative
or not (i.e. do you end up with twelve partridges, or just one?). Sadly, this
program doesn't really answer the question, any more than singing it would.

{% highlight python %}
gifts = ['Partridge in a pear tree', 'Turtle doves', 'French hens',
'Calling birds', 'Golden rings', 'Geese-a-Laying',
'Swans-a-Swimming', 'Maids-a-Milking', 'Ladies Dancing',
'Lords-a-Leaping', 'Pipers Piping', 'Drummers Drumming']

def days_of_christmas(how_many):
    for day in range(how_many):
        print 'On the %s day of Christmas, my true love gave to me:' % ordinal(day)
        for number in range(day, -1, -1):
            print '%s %s%s' % (named(number), gifts[number], ',' if number else '.')

def ordinal(num):
    return ('first','second','third','fourth','fifth','sixth',
            'seventh','eighth','ninth','tenth','eleventh','twelfth')[num]

def named(num):
    return ('a','two','three','four','five','six',
            'seven','eight','nine','ten','eleven','twelve')[num]

days_of_christmas(12)
{% endhighlight %}

It turns out you get 364 gifts if they are cumulative, which, oddly enough, is
exactly enough for you to get a present every day for a year *except*
Christmas Day. Coincidence?

