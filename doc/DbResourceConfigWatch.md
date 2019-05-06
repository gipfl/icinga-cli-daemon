DbResourceConfigWatch
=====================

This is a very simple implementation watching Icinga Web/Cli DB resource config
files for changes. No INOTIFY or similar involved, this just re-reads your
configuration every 3 seconds. In case the resource configuration changed, you'll
get notified.

Usage
-----

In case you want to watch a specific resource by name, just run as follows:

```php
DbResourceConfigWatch::name($resourceName)
    ->notify(function (array $config = null) {
        // Reconnect to your database
    });
```

As soon as a modifiey resource configuration has been detected, your callback
method will be called. Only parameter is the resource configuration as a plain
PHP array. Be prepared to get passed `null`, as the configuration might have
been deleted.

In case there is no configuration from the beginning, your callback will never
be fired.

Usage for an Icinga Web 2 module
--------------------------------

Often Icinga Web 2 modules allow to configure a specific DB resource name. This
means that we should also watch this module's configuration, eventually the
resource name might have been changed. This class wants to simplify this use-case
and provides a dedicated helper method:

```php
DbResourceConfigWatch::module($moduleName)->notify($callback);
```

This assumes a `config.ini` in your module's config directory (usually to be
found in `/etc/icingaweb2/modules/<moduleName>/config.ini`) structured as follows:

```ini
[db]
resource = "DB Resource Name"
```
