#a PHP CLI/Library for the VersionEye API

see https://www.versioneye.com/api/ for API documentation

[![Build Status](https://img.shields.io/travis/digitalkaoz/versioneye-php/master.svg?style=flat-square)](https://travis-ci.org/digitalkaoz/versioneye-php)
[![Dependency Status](https://img.shields.io/versioneye/d/php/digitalkaoz:versioneye-php.svg?style=flat-square)](https://www.versioneye.com/php/digitalkaoz:versioneye-php)
[![Scrutinizer Code Quality](https://img.shields.io/scrutinizer/g/digitalkaoz/versioneye-php.svg?style=flat-square)](https://scrutinizer-ci.com/g/digitalkaoz/versioneye-php/?branch=master)
[![Code Coverage](https://img.shields.io/scrutinizer/coverage/g/digitalkaoz/versioneye-php/master.svg?style=flat-square)](https://scrutinizer-ci.com/g/digitalkaoz/versioneye-php/?branch=master)
[![SensioLabsInsight](https://img.shields.io/sensiolabs/i/f7633a7e-4577-4a86-b6d9-ccaa75cb7fa0.svg?style=flat-square)](https://insight.sensiolabs.com/projects/f7633a7e-4577-4a86-b6d9-ccaa75cb7fa0)
[![Latest Stable Version](https://img.shields.io/packagist/v/digitalkaoz/versioneye-php.svg?style=flat-square)](https://packagist.org/packages/digitalkaoz/versioneye-php)
[![Total Downloads](https://img.shields.io/packagist/dt/digitalkaoz/versioneye-php.svg?style=flat-square)](https://packagist.org/packages/digitalkaoz/versioneye-php)
[![StyleCI](https://styleci.io/repos/23851520/shield)](https://styleci.io/repos/23851520)

##Installation

There are 2 ways to install it: 
 
  - Download the Phar (recommended)
  - Install from source code

### Download the Phar (recommended)

download the latest version from the [Releases section](https://github.com/digitalkaoz/versioneye-php/releases/latest) or from the cli:

```
$ wget https://github.com/digitalkaoz/versioneye-php/releases/download/0.11.2/versioneye.phar //or latest stable
```

### Install as global Composer Package

```
$ composer g require digitalkaoz/versioneye-php
```

now you can run `~/.composer/vendor/bin/versioneye` maybe add this folder to your `PATH` variable.

### Install from source code

first you have to decide which `http adapter` to use. The library supports all adapters supported by [egeloen/ivory-http-adapter](https://github.com/egeloen/ivory-http-adapter)
Where `fopen` is last resort if even `curl` is missing.

```
$ composer require digitalkaoz/versioneye-php
```

##Usage

all API endpoints are implemented, see https://www.versioneye.com/api/ for their detailed docs.


### programmatic:

```php
<?php

use Rs\VersionEye\Client;

$api = (new Client())->api('services');     // Rs\VersionEye\Api\Services
$api->ping(); //array

//other implemented APIs
$api = (new Client())->api('github');       // Rs\VersionEye\Api\Github
$api = (new Client())->api('me');           // Rs\VersionEye\Api\Me
$api = (new Client())->api('projects');     // Rs\VersionEye\Api\Projects
$api = (new Client())->api('products');     // Rs\VersionEye\Api\Products
$api = (new Client())->api('sessions');     // Rs\VersionEye\Api\Sessions
$api = (new Client())->api('users');        // Rs\VersionEye\Api\Users

```

### cli:

Here some usage examples.

```
$ bin/versioneye services:ping
$ bin/versioneye products:search symfony
```

Or with the phar file. 

```
php versioneye.phar products:search "symfony"
php versioneye.phar products:show "php" "symfony:symfony"
```

The last command requires that you have setup your [API Key](https://www.versioneye.com/settings/api) correctly. 


##Configuration

to store your [generated API Token](https://www.versioneye.com/settings/api) globally you can create a global config file in your home directory:

`~/.veye.rc` we share the same config file with the ruby cli https://github.com/versioneye/veye

the file would look like:

```rc
:api_key: YOUR_API_TOKEN
```

now you dont have to pass your token on each call!


##CLI Tool

to build a standalone phar, simply execute the following commands.

```
$ composer require --dev kherge/box
$ vendor/bin/box build
$ php versioneye.phar
```

## Commands:

The Commands are autogenerated by introspecting the API Implementations. Each Public Method is a Command, each Method Parameter will be translated into a InputArgument or InputOption.


     github
      github:delete           remove imported project.
      github:hook             GitHub Hook.
      github:import           imports project file from github.
      github:repos            lists your's github repos.
      github:show             shows the detailed information for the repository.
      github:sync             re-load github data.
     me
      me:comments             shows comments of authorized user.
      me:favorites            shows favorite packages for authorized user.
      me:notifications        shows unread notifications of authorized user.
      me:profile              shows profile of authorized user.
     products
      products:follow         follow your favorite software package.
      products:follow_status  check your following status.
      products:references     shows all references for the given package.
      products:search         search packages.
      products:show           detailed information for specific package.
      products:unfollow       unfollow given software package.
      products:versions       shows all version for the given package.
     projects
      projects:all            shows user`s projects.
      projects:create         upload project file.
      projects:delete         delete given project.
      projects:licenses       get grouped view of licences for dependencies.
      projects:merge          merge two projects together.
      projects:merge_ga       merge two projects together (only for maven projects).
      projects:show           shows the project's information.
      projects:unmerge        unmerge two projects.
      projects:update         update project with new file.
     services
      services:ping           Answers to request with basic pong.
     sessions
      sessions:close          delete current session aka log out.
      sessions:open           creates new sessions.
      sessions:show           returns session info for authorized users.
     users
      users:comments          shows user's comments.
      users:favorites         shows user's favorite packages.
      users:show              shows profile of given user_id.

## FAQ

### implement a new HTTP Adapter

simply implement the `Rs\VersionEye\Http\HttpClient` Interface:

```php
<?php
class MyHttpClient implements HttpClient
{
    /**
     * @inheritDoc
     */
    public function request($method, $url, array $params = [])
    {
        //implement your own special http handling here
    }
}
```

and then pass it the the Client:

```php
<?php 

$api = (new Client(new MyHttpClient))->api('users');
```

### writing a new Api

simply implement the `Rs\VersionEye\Api\Api` Interface:

```php
<?php
namespace Rs\VersionEye\Api;

class Foo implements Api
{
    /**
     * awesome api endpoint
     */
    public function bar($bar, $bazz=1)
    {
        //implement api endpoint
    }
}
```

the you have to register the Api in the `CommandFactory` (maybe even that could be autogenerated by searching all implementors of Interface `Rs\VersionEye\Api\Api`):

```php
<?php 
class CommandFactory
{
    /**
     * generates Commands from all Api Methods
     *
     * @param  array     $classes
     * @return Command[]
     */
    public function generateCommands(array $classes = [])
    {
        $classes = $classes ?: [
            //...
            'Rs\VersionEye\Api\Foo'
        ];
    }
}    
``` 
be aware that each public method would be exposed as `Command`. Mandatory Parameters will be `InputArgument`s, optionals will be a `InputOption`. The Command description would be taken from the `phpdoc`!

So the above example will be generated to this `Command`:

    foo:bar --bazz=1 bar      //awesome api endpoint


### Writing a new Console Output Formatter

by default the `Command` tries to find the same API method in the Output Classes (if not it will output the data as simple `print_r`:

`Rs\VersionEye\Api\Github:sync` **API** ----> `Rs\VersionEye\Output\Github:sync` **Output**

so for the above Example simply create the following Class:

```php
<?php

namespace Rs\VersionEye\Output;

class Foo
{
    public function bar(OutputInterface $output, $response)
    {
        //output the $response (API Result)
    }
}
```

thats all.

##Tests

```
$ composer require --dev henrikbjorn/phpspec-code-coverage
$ vendor/bin/phpspec run
```
