# Example configuration of php-fpm

## Introduction 

This is an example configuration [`php-fpm`](http://www.php.net/manual/en/install.fpm.php)
which is the preferred way of serving PHP with FastCGI. The old
`php-cgi` is deprecated. You can even use a
[patch](http://php-fpm.org) for PHP versions prior to 5.3.3, when fpm
got merged to main PHP tree.


## Generalities

The way that php-fpm operates is through a **master** process that
launches children processes (workers) that serve the requests. These
workers can be run under a particular user. Making it appropriate for
environments where there are several users each one running their own
set of PHP apps.

php-fpm is pretty much a **work in progress**, although on one hand is
IMHO the best thing to come out of PHP land in a lot of time, still
lacks a lot of nice features like **graceful restarts**. Currently
adding a new pool or reloading the config requires a **full
restart**. Which causes some downtime. Work is in progress to provide
graceful restarts like
[Nginx](http://wiki.nginx.org/NginxCommandLine).

It has the capacity to adjust the number of workers **dynamically** to
the load, varying from a minimum to a specified maximum.

I've seen a formula being passed around that supposedly is the *right*
thing.

The formula is: 
  
  number\_of\_children = (total_memory - 512 MB) / 128 MB
    
it's implied that each child consumes up to **128 MB**.

Using this formula we get numbers significantly below the defaults
that come with the distribution packages like the one provided by
Debian and that is included here for completeness and providing a term
of comparison. It's named `php5-fpm.conf.dpkg-dist`.

This is the configuration I use for local development you should
adjust it to your setup and conditions if usage. 


## Features 

 1. It uses a `UNIX` socket for connections from the web server to the
    FastCGI daemon.
    
 2. The `php.ini` is modified from the stock one that comes with the
    Debian package. The modifications were made by using a tiny script
    that I wrote for *cleaning up* a PHP config and that's available
    [here](https://github.com/perusio/php-ini-cleanup) on
    github.
 
 3. There's a single pool on this config that is run under the
    `www-data` user.
    
 4. Support for the **status** and **ping** functionalities of
    `php-fpm`. See
    [here](https://github.com/perusio/drupal-with-nginx) how to enable
    it for [Nginx](http://wiki.nginx.org).
    

## Installation

 1. Clone the git repo:
    `git://github.com/perusio/php-fpm-example-config`.
    
 2. Alter the `php-fpm.conf` and the `pool.d/www.conf` file to your
    liking. Add any pool that you might want.
    
 4. Copy the files to the destination directory:
 
     cp php5-fpm.conf /etc/php5/fpm
     
     cp -a pool.d /etc/php5/fpm
     
 3. (Re)start `php-fpm` with `service php-fpm restart` or `service
    php-fpm start` if starting `php-fpm` anew.   

## Caveats

Remember to **always** do `service php5-fpm restart` after adding a
new pool or modifying the configuration of an existing one.
