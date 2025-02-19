# Mosquitto-PHP

This is an extension to allow using the [Eclipse Mosquitto™ MQTT client library](https://mosquitto.org) with PHP. See the `examples/` directory for usage. This is a fork of the project [Mosquitto-PHP](https://github.com/mgdm/Mosquitto-PHP) by Michael Maclean.

## Requirements

* PHP 8.0+
* libmosquitto 1.2.x or later
* Linux or Mac OS X. I do not have a Windows machine handy, though patches or
  pull requests are of course very welcome!

## Installation

If you've used a pre-built package to install Mosquitto, you need to make sure you have the development headers installed. On Red Hat-derived systems, this is probably called `libmosquitto-devel`, and on Debian-based systems it will be `libmosquitto-dev`.

````
phpize
./configure --with-mosquitto=/path/to/libmosquitto
make
make install
````

Then add `extension=mosquitto.so` to your `php.ini`.

The `--with-mosquitto` argument is optional, and only required if your
libmosquitto install cannot be found.

## General operation

The underlying library is based on callbacks and asynchronous operation. As such, you have to call the `loop()` method of the `Client` frequently to permit the library to handle the messages in its queues. Also, you should use the callback functions to ensure that you only attempt to publish after the client has connected, etc. For example, here is how you would correctly publish a QoS=2 message:

```php
<?php

use Mosquitto\Client;

$mid = 0;
$c = new Mosquitto\Client("PHP");
$c->onLog('var_dump');
$c->onConnect(function() use ($c, &$mid) {
    $mid = $c->publish("mgdm/test", "Hello", 2);
});

$c->onPublish(function($publishedId) use ($c, $mid) {
    if ($publishedId == $mid) {
        $c->disconnect();
    }
});

$c->connect("localhost");
$c->loopForever();

echo "Finished"
```

## Documentation

Full documentation is [available on ReadTheDocs](http://mosquitto-php.readthedocs.io/).

