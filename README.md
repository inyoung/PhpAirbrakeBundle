PHP Airbrake Bundle for Symfony2
================================

This helps binds the [php-airbrake module](https://github.com/nodrew/php-airbrake) into a Symfony2 bundle for easy use with the framework. It will autoload an exception handler into the framework, so that all uncaught errors are sent to the [Airbrake Service](http://airbrake.io).

Installation Instructions
=========================

Add these blocks to the following files

*deps*

```
[PhpAirbrakeBundle]
    git=http://github.com/AbstractCodification/PhpAirbrakeBundle.git
    target=/bundles/AbstractCodification/Bundle/PhpAirbrakeBundle
    
[php-airbrake]
    git=http://github.com/AbstractCodification/php-airbrake.git
    target=/bundles/AbstractCodification/Bundle/PhpAirbrakeBundle/vendor/php-airbrake
```

*app/autoload.php*

```
$loader = new UniversalClassLoader();
$loader->registerNamespaces(array(
    ...
    'AbstractCodification'   => __DIR__.'/../vendor/bundles',
    ...
));
```

*app/AppKernel.php*

```
public function registerBundles()
{
    $bundles = array(
        // System Bundles
        ...
        new AbstractCodification\Bundle\PhpAirbrakeBundle\PhpAirbrakeBundle(),
        ...
    );
}
```

*app/config/config.yml*

```
php_airbrake:
    api_key: [your api key]
    queue: [optional resqueue queue name]
```

Calling Airbrake Manually
=========================

The [Airbrake Client](https://github.com/nodrew/php-airbrake) can be found inside the Service Container and can be used like this:

```php
<?php
$client = $container->get('php_airbrake.client');

$client->notifyAboutError('Something really bad happened!');
$client->notifyAboutException(new Exception('Why did I catch this? It would have been caught on its own!?!'));
```

Resque Integration
==================

This client will allow for integration with PHPResque by providing the name of the Resque queue to add your error into. It is advisable that if you want to use PHPResque with Symfony2, that you use the [PHPResqueuBundle](https://github.com/hlegius/PHPResqueBundle) from [hlegius](https://github.com/hlegius), as it makes interfacing the worker processes with Symfony2 a breeze.

Once this is installed and running, simply fill in the *queue* config variable with the name of the queue you would like to use. I suggest just keeping it simple with something like 'airbrake'. Assuming you have a worker process running for this queue, you should be golden.