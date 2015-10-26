
Version 0.8 of times2 is based on original `times` library developed by
Vincent Driessen, but slighltly modified and **without** Arrow
dependency.

The main assumption is to provide library which operates on native
and tz-aware datetime objects.  Arrow is a great library but introduces
new type, which is incompatible with datetime interface and can't be
used as a simple replacement - there are conversion steps needed.

The goal of times2 is to provide a useful set of helpers which operates on
standard datetime objects.

---

Times2
======

Build status:
[![Build Status](https://travis-ci.org/marcinn/times2.svg?branch=master)](http://travis-ci.org/marcinn/times2)
[![Coverage Status](https://img.shields.io/coveralls/marcinn/times2.svg)](https://coveralls.io/r/marcinn/times2?branch=master)


Times2 is a small, minimalistic, Python library for dealing with time
conversions to and from timezones, for once and for all.


Accepting time
--------------

Never work with _local_ (naive) times.  Whenever you must accept local time input (e.g.
from a user), convert it to universal (or aware) time immediately:

```pycon
>>> times2.to_universal(datetime.datetime(2015,10,26,10,20,0), 'Europe/Warsaw')
datetime.datetime(2015, 10, 26, 9, 20, tzinfo=<UTC>)
```

The second argument can be a `pytz.timezone` instance, or a timezone string.
Leaved empty will use output of `times2.local_tz()`

If the `local_time` variable already holds timezone info, you _must_ leave out
the source timezone from the call.


Date Strings
------------
If you want to accepting datetime representations in string form (for example,
from JSON APIs), you can convert them to universal datetimes easily:

```pycon
>>> import time, times2
>>> print times2.to_universal('2012-02-03 11:59:03-0500')   # auto-detects source timezone
```

`Times` utilizes the string parsing routines available in [dateutil][1].  Note
that the source timezone is auto-detected from the string.  If the string
contains a timezone offset, you are not allowed to explicitly specify one.

If the string does not contain any timezone offset, you _must_ specify the
source timezone explicitly:

```pycon
>>> print times2.to_universal('2012-02-03 11:59:03', 'Europe/Amsterdam')
```

This is the inverse of `times2.format()`.


POSIX timestamps
----------------
If you prefer working with UNIX (POSIX) timestamps, you can convert them to
safe datetime representations easily:

```pycon
>>> import time, times2
>>> print times2.to_universal(time.time())
datetime.datetime(2015, 10, 26, 14, 28, 7, 283998, tzinfo=<UTC>)
```

Note that `to_universal` auto-detects that you give it a UNIX timestamp.

To get the UNIX timestamp representation of any tz-aware datetime, use:

```pycon
>>> print times2.to_unix(universal_time)
```

Naive datetimes are not supported. You must convert them to aware
datetimes before using `to_unix()`.

Current time
------------

When you want to record the current time, you can use this convenience method:

```pycon
>>> import times2
>>> times2.now()
datetime.datetime(2015, 10, 26, 14, 38, 41, 871750, tzinfo=<UTC>)
```

Presenting times
----------------

To _present_ times to the end user of your software, you should explicitly
format your universal time to your user's local timezone.

```pycon
>>> import times
>>> now = times2.now()
>>> print times2.format(now, 'CET')
2012-02-01 21:32:10+0100
```

As with the `to_universal` function, the second argument may be either
a timezone instance or a timezone string.

**Note**: It _is_ possible to convert universal times to local times, using
`to_local`).  However, you probably shouldn't do it, unless you want to
`strftime()` the resulting local date multiple times.  In any other case, you
are advised to use `times.format()` directly instead.

[1]: http://labix.org/python-dateutil#head-c0e81a473b647dfa787dc11e8c69557ec2c3ecd2
