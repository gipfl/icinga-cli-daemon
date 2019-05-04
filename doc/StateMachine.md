StateMachine
============

This minimal State Machine has been implemented as a `trait` and can be used as
such in your code.

Basic Usage
-----------

First of all, `use` the `StateMachine` in your class:

```php
<?php

use gipfl\IcingaCliDaemon\StateMachine;

class MyDaemon
{
    use StateMachine;
}
```

In your constructor or in a custom setup method define all possible transitions.
This is done via the `onTransition()` method. It accepts one or more source
states and a single targetState. The third parameter is a callback to be triggered
whenever a matching transition occurs.

```php
$this->onTransition(['started', 'failed'], 'ready', function () {
    // We are now ready, let's run our main tasks
})->onTransition('ready', 'failed', function () {
    // Something failed. Re-run initialization
});
```

Once all possible transitions have been defined you need to start your
`StateMachine`:

```php
$this->initializeStateMachine('started');
```

This only sets the current state, no transition is going to be triggered. From
now on you can switch the current state at any time via `setState()`:

```php
$this->setState('ready');
```

All related transition callbacks will now be triggered. Please note that this
will throw an Exception in case no possible transition from the current to that
new state has formerly been defined.

You might sometimes want to permit certain transitions without triggering any
related action. This is where the `allowTransition()` method comes in handy:

```php
$this->allowTransition('disconnected', 'failed');
```

This is often useful in combination with `onState()`, a method that allows you
to run a callback when a specific state has been reached, regardless of where
you came from.
