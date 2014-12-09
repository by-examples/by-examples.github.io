---
layout: post
title: Running Symfony2 app on Vagrant without NFS below 100ms
excerpt: |
    This post explains how to configure <strong>Symfony2/Vagrant</strong>
    to get the response time below 100 ms.
    And how to do it <strong>without NFS</strong>!
    This is, in my opinion, <strong>the best way
    to run Windows/Vagrant/Symfony2</strong> combination.
---

##1. Background

If you try to run Symfony2 application within Vagrant
box on Windows you will, most probably,
get page load time quite close to 5 or 10 seconds.
Which, of course, is unbearable.

This is the starting point:

* we have a Symfony2 application
* the app runs in development env created with Vagrant
* host for Vagrant boxes runs Windows

The task is:
<strong>how to configure development machine to minimize the page load time?</strong>

That's really important for me, as the greater part of young
developers I educate use Windows.

##2. The solution

My solution is very simple: move `vendor/`
outside shared project's folder.

**Yes, as simple as that.**

<div class="alert alert-info" role="alert">
<strong>
<i class="fa fa-thumbs-o-up"></i>
To get a huge speed up of your Windows/Vagrant/Symfony2 application
page load time move <code>vendor/</code> folder somewhere inside native filesystem
of VM.
</strong>
</div>

To do this, you need to do two simple changes:

* change destination folder for `vendor/` in `composer.json`
* change the path to `autoload.php` file in `app/autoload.php`

Here is the the change you have to introduce in `composer.json` file:

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

Even if you do not apply any other optimizations, you
will get an overwhelming speedup.

##3. Reason

Why does this change leads to such amazing results?
Vagrant offers very poor I/O performance of
on shared folder.
This is a well known feature of virtualized solutions.
You can find more details in the post
<a href="http://mitchellh.com/comparing-filesystem-performance-in-virtual-machines">Comparing Filesystem Performance in Virtual Machines</a>.

The analysis and benchmarks of Vagrant/Symfony app
is available in the post titled
<a href="http://www.erikaheidi.com/blog/optimizing-symfony-applications-vagrant-boxes">
Optimizing Symfony applications on Vagrant boxes
</a>.


##4. Other optimizations concerning page load time

Of course, it is worth your while to implement other well known
solutions. Thanks to the excellent post
<a href="http://www.whitewashing.de/2013/08/19/speedup_symfony2_on_vagrant_boxes.html">
Speedup Symfony2 on Vagrant boxes</a>
you should already know, how to move `cache/` and `logs/`
to other location, `/dev/shm`, for example.
The official documentation contains a cookbook entry that you may also want to take at look at:
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

Another good starting point for pursuing Symfony2 optimization
is a post titled
<a href="http://stackoverflow.com/questions/12905404/symfony2-slow-initialization-time">
Symfony2 Slow Initialization Time
</a>
on stackoverflow.
There you will find info about changing `php.ini` settings:

* `realpath_cache_size = 4096k`
* `realpath_cache_ttl=7200`

##5. Boxed solution - one more trick

If you work with Symfony2/Vagrant I have one more trick to share.
It is also very simple and leads to substantial time saving.
It doesn't affect page time load, but the time
you need to run:

    $ composer install

command. The trick is: cache dependencies in your box.

<div class="alert alert-info" role="alert">
<strong>
<i class="fa fa-thumbs-o-up"></i>
When you prepare the box remember to cache the dependencies inside your box.
</strong>
</div>

How to do it?
When you create a boxed solution with:

    $ vagrant package --output symfony-box-v1.2.3.box

try to run, just before the above command, the composer:

     $ composer install

Do it in arbitrary directory and use the `composer.json`
for the latest Symfony Standard, for example.

This trick is important if you start a new project quite often.
During my classes, I start a new project with every group of students
once a week. By caching dependencies we save a lot of time (and nerves).

<div class="alert alert-info" role="alert">
<strong>
<i class="fa fa-thumbs-o-up"></i>
First of all you need to work with boxed solution
that doesn't run provisioners during first booting.
</strong>
</div>

##6. Summary

Here are the tricks, that have made my Windows/Vagrant/Symfony2 classes bearable:

* use boxed solution (more examples on this will follow shortly)
* cache composer dependencies inside your box
* move `vendor/` directory outside `/vagrant` folder
* move `cache/` and `logs/` directories outside `/vagrant`
* chance `realpath_*` settings in `php.ini`

<div class="alert alert-warning" role="alert">
With the above tricks:

<ul>
    <li>you can start a new project in less then a minute (assuming that you have already downloaded a box)</li>
    <li>page load time can be below 50 ms</li>
</ul>
</div>



##7. Reading list

1. Mitchell Hashimoto: <a href="http://mitchellh.com/comparing-filesystem-performance-in-virtual-machines">Comparing Filesystem Performance in Virtual Machines</a>
2. Erika Heidi: <a href="http://www.erikaheidi.com/blog/optimizing-symfony-applications-vagrant-boxes">Optimizing Symfony applications on Vagrant boxes</a>
3. Benjamin Eberlei: <a href="http://www.whitewashing.de/2013/08/19/speedup_symfony2_on_vagrant_boxes.html">Speedup Symfony2 on Vagrant boxes</a>
4. orourkedd, Dénes Papp <a href="http://stackoverflow.com/questions/12905404/symfony2-slow-initialization-time">Symfony2 Slow Initialization Time</a>