---
layout: post
title: Running the Symfony application on Vagrant without NFS below 100ms
excerpt: |
    This post explains how to configure <strong>Symfony/Vagrant</strong>
    to get response time below 100 ms.
    And how to do it <strong>without NFS</strong>!
    This is, in my opinion, <strong>the best way
    to run the Windows/Vagrant/Symfony</strong> combination.
---

##1. Background

If you try to run the Symfony application within the Vagrant
box on Windows you will, most probably,
get a page load time of almost 5 or 10 seconds.
Which, of course, is unbearable.

This is the starting point:

* we have a Symfony application
* the application runs in development environment created with Vagrant
* the host for Vagrant boxes runs Windows

The task is:
<strong>how to configure the development machine to minimise the page-load time?</strong>

That's really important for me, as the majority of young
developers who I educate use Windows.

##2. The solution

My solution is very simple: move `vendor/`
outside shared project's folder.

**Yes, it's as simple as that.**

<div class="alert alert-info" role="alert">
<strong>
<i class="fa fa-thumbs-o-up"></i>
To achieve a huge speed-up of your Windows/Vagrant/Symfony application
page load time move the <code>vendor/</code> folder somewhere inside the native filesystem
of the VM.
</strong>
</div>

To do this, you need to make two simple changes:

* change the destination folder for vendors to `vendor/` in `composer.json`
* change the path leading to the `autoload.php` file in `app/autoload.php`

Here is the first change you have to introduce in the `composer.json` file:

    //filename: composer.json
    {
        ...
        "config": {
            "bin-dir": "bin",
            "vendor-dir": "/home/vagrant/symfony2app/vendor"
        },
        ...
    }

And this is the second change:

    //filename: app/autoload.php
    /**
     * @var ClassLoader $loader
     */
    $loader = require '/home/vagrant/symfony2app/vendor/autoload.php';

Now, you have to install dependencies:

    $ composer install

And... enjoy!

Even if you don't apply any other optimisations, you
will get a substantial speed-up.

##3. Reason

Why does this change lead to such amazing results?
Vagrant has a very poor I/O performance of on shared folder.
This is a well known feature of virtualized solutions.
You can find more details in the post
<a href="http://mitchellh.com/comparing-filesystem-performance-in-virtual-machines">Comparing Filesystem Performance in Virtual Machines</a>.

The analysis and benchmarks of the Vagrant/Symfony application
is available in the post entitled
<a href="http://www.erikaheidi.com/blog/optimizing-symfony-applications-vagrant-boxes">
Optimizing Symfony applications on Vagrant boxes</a>.


##4. Other optimizations

Of course, it is worth your while to implement other well-known
solutions. Thanks to the excellent post
<a href="http://www.whitewashing.de/2013/08/19/speedup_symfony2_on_vagrant_boxes.html">
Speedup Symfony2 on Vagrant boxes</a>
you should already know, how to move `cache/` and `logs/`
to other location, `/dev/shm`, for example.
The official documentation contains a cookbook entry that you might also want to take a look at:
<a href="http://symfony.com/doc/current/cookbook/configuration/override_dir_structure.html">
How to Override Symfony's default Directory Structure</a>.

<div class="alert alert-info" role="alert">
<strong>
<i class="fa fa-thumbs-o-up"></i>
Another important trick is to move
<code>cache/</code> and <code>logs/</code> folders
into the VM's native filesystem.
</strong>
</div>

Another good starting point for pursuing Symfony optimisation
is the post entitled
<a href="http://stackoverflow.com/questions/12905404/symfony2-slow-initialization-time">
Symfony2 Slow Initialization Time
</a>
on StackOverflow.
There you will find info about changing `php.ini` settings:

* `realpath_cache_size = 4096k`
* `realpath_cache_ttl=7200`

##5. The boxed solution - one more trick

If you work with Symfony/Vagrant I have one more trick to share.
It is also very simple and leads to substantial time saving.
It doesn't affect page-load time, but it does reduce the time
you need to run the:

    $ composer install

command. The trick is: cache dependencies in your box.

<div class="alert alert-info" role="alert">
<strong>
<i class="fa fa-thumbs-o-up"></i>
When you prepare the box remember to cache the dependencies inside your box.
</strong>
</div>

How do you do it?
When you create a boxed solution with:

    $ vagrant package --output symfony-box-v1.2.3.box

try to run, just before the above command, the composer:

     $ composer install

Do it in an arbitrary directory and use the `composer.json`
for the latest Symfony Standard, for example.

This trick is important if you start new projects quite often.
During my lessons, I start a new project with every group of students
once a week. By caching dependencies we save a lot of time (and nerves!).

<div class="alert alert-info" role="alert">
<strong>
<i class="fa fa-thumbs-o-up"></i>
First of all you need to work with a boxed solution
that doesn't run provisioners during the first booting.
</strong>
</div>

##6. Summary

Here are the tricks, that have made my Windows/Vagrant/Symfony classes viable:

* use a boxed solution (more examples on this will follow shortly)
* cache the composer dependencies inside your box
* move the `vendor/` directory outside the `/vagrant` folder
* move the `cache/` and `logs/` directories outside `/vagrant`
* change `realpath_*` settings in `php.ini`

<div class="alert alert-warning" role="alert">
With the above tricks:

<ul>
    <li>you can start a new project in less than a minute (assuming you have already downloaded a box)</li>
    <li>page-load time can be below 50 ms</li>
</ul>
</div>

##7. Reading list

1. Mitchell Hashimoto: <a href="http://mitchellh.com/comparing-filesystem-performance-in-virtual-machines">Comparing Filesystem Performance in Virtual Machines</a>
2. Erika Heidi: <a href="http://www.erikaheidi.com/blog/optimizing-symfony-applications-vagrant-boxes">Optimizing Symfony applications on Vagrant boxes</a>
3. Benjamin Eberlei: <a href="http://www.whitewashing.de/2013/08/19/speedup_symfony2_on_vagrant_boxes.html">Speedup Symfony2 on Vagrant boxes</a>
4. orourkedd, Dénes Papp <a href="http://stackoverflow.com/questions/12905404/symfony2-slow-initialization-time">Symfony2 Slow Initialization Time</a>
