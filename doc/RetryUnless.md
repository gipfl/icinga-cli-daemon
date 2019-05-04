RetryUnless
===========

It is often necessary to regularly retry a specific task unless it succeeds (or
fails). That's what this little class has been built for. Please do not use this
in case you need something to be scheduled at a precise periodic interval. Use
this if you need to retry code blocks in a smooth and fail-safe way.

The main use-case for this are connection attempts to backend systems like
databases. If you want to develop a fail-safe daemon dealing with connection
errors, trying to re-connect unless it succeeds, this is the library you're
looking for.

Let's prepare some callback functions:

```php
$connect = function () { /* Try to connect to a database */ };
$onReady = function () { echo 'We finally succeeded!' };
```

Now let's call that `$callback` every 5 seconds unless it succeeds:

```php
RetryUnless::succeeding($callback)
   ->setInterval(5)
   ->run($this->loop)
   ->then($onReady);
```

Scheduling
----------

This does not behave like a periodic timer, even if it looks and feels similar.
The next attempt is (intentionally) always scheduled based on the given interval
**after** the former one failed. So in case you're running blocking long-running
code this will have an influence on your schedule.

When your first attempts starts at 03:00 and takes 3 seconds unless it fails,
given a 5-second interval the next attempt will be scheduled for 03:08.

Burst: start fast, slow down
----------------------------

One might want to first run a couple of faster attempts and then fall back to
retry from time to time at a slower pace. The following example could be used
to try to reconnect to a database 5 times a second (every 0.2 seconds). In case
the first 5 attempts fail, it will still try to reconnect forever - but only
once every 15 seconds:

```php
RetryUnless::succeeding($callback)
   ->setInterval(0.2)
   ->slowDownAfter(5, 15)
   ->run($this->loop)
   ->then($onReady);
```

Run periodically unless it fails
--------------------------------

Above examples retry unless the given callback succeeds. You can also use the
inverted logic, re-runnig a callback at a given interval unless it fails:

```php
RetryUnless::failing($callback)
    ->setInterval(5)
    ->run($loop)
    ->then($fail);
```

Please make sure to understand the difference between this and `addPeriodicTimer`
before you start using it. Hint: read the **Scheduling** section.
