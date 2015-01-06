---
layout: post
title: "Puppet module <code>filefetcher</code>"
excerpt: |
    The module I want to present facilitates installation
    of various <strong>one-file-applications<strong>, like
    <code>composer</code>, <code>phpunit</code>,
    <code>symfony</code> installer, <code>box</code>,
    <code>php-cs-fixer</code>,
    and many others.

---

<div class="alert alert-success text-center">
<strong>
<i class="fa fa-thumbs-o-up"></i>
You will find
<a href="https://github.com/pro-vagrant/puppet-filefetcher">the source code of the
<code>filefetcher</code> module </a> on GitHub.
</strong>
</div>

<div class="alert alert-success text-center">
<strong>
<i class="fa fa-thumbs-o-up"></i>
<a href="https://forge.puppetlabs.com/gajdaw/filefetcher">Binary version of <code>filefetcher</code> module</a>
is available on Puppet Forge.
</strong>
</div>

##1. Introduction

During last year or two the php world has changed. More and more
applications are distributed as phar archives. The most frequently used are

* `composer`
* `phpunit`
* `php-cs-fixer`
* `box`

but there are others to follow

* `symfony` installer

The installation of such an application consists of:

* `wget | curl`
* `mv`
* `chown`

Well. And that's exactly what `filefetcher` module helps to do.
The module defines `filefetcher::fetch` resource which:

* downloads a file using URL parameter (e.g. `https://getcomposer.org/composer.phar`)
* saves it under a given name (e.g. `composer`) in an arbitrary directory (e.g. `/usr/local/bin`)
* sets the owner (e.g. `root`)
* sets the rights (e.g. `a+x`)

##2. How to use `filefetcher` puppet module?

###2.1. System wide install with Puppet

To install the module in your system run:

    sudo puppet module install gajdaw-filefetcher

You may lock the version to avoid using the latest version:

    sudo puppet module install gajdaw-filefetcher --version 0.1.0

###2.2. System wide install with Git

You may also use `git` to install the module:

    mkdir -p /etc/puppet/modules/box
    cd /etc/puppet/modules/box
    git clone --depth 1 https://github.com/pro-vagrant/puppet-filefetcher.git .

To lock the version, use:

    git clone --depth 1 --branch v0.1.0 https://github.com/pro-vagrant/puppet-filefetcher.git .

##3. Usage examples

The examples are stored under `examples/` directory.

###3.1. Composer

Here is `composer.pp` example:

    # Filename: examples/composer.pp
    filefetcher::fetch { 'composer':
        source => 'https://getcomposer.org/composer.phar',
    }

To run it use the following command:

    sudo puppet apply examples/composer.pp

Try to run the above command a number of times:

    sudo puppet apply examples/composer.pp
    sudo puppet apply examples/composer.pp
    sudo puppet apply examples/composer.pp

As you can see the file is not downloaded if it already exists.
You can change this behaviour with `redownload` parameter,
as described in `phpunit` example.

###3.2. Phpunit

Here is `phpunit.pp` example:

    # Filename: examples/phpunit.pp
    filefetcher::fetch { 'phpunit':
        source     => 'https://phar.phpunit.de/phpunit.phar',
        redownload => true,
    }

Thanks to `redownload => true` the file `phpunit` will be
redownloaded even if it exists.

To run it use the following command:

    sudo puppet apply examples/phpunit.pp

Now the file is downloaded every time you run the command:

    sudo puppet apply examples/phpunit.pp
    sudo puppet apply examples/phpunit.pp
    sudo puppet apply examples/phpunit.pp

###3.3. Php-cs-fixer

Here is `php-cs-fixer.pp` example:

    # Filename: examples/php-cs-fixer.pp
    filefetcher::fetch { 'php-cs-fixer':
        source => 'http://cs.sensiolabs.org/get/php-cs-fixer.phar',
    }

To run it use the following command:

    sudo puppet apply examples/php-cs-fixer.pp

###3.4. Box

Here is `box.pp` example:

    # Filename: examples/box.pp
    filefetcher::fetch { 'box':
        source => 'https://github.com/box-project/box2/releases/download/2.5.0/box-2.5.0.phar',
    }

To run it use the following command:

    sudo puppet apply examples/box.pp

###3.5. Symfony Installer

And `symfony-installer.pp` example:

    # Filename: examples/symfony-installer.pp
    filefetcher::fetch { 'symfony':
        url => 'http://symfony.com/installer',
    }

To run it use the following command:

    sudo puppet apply examples/symfony-installer.pp

###3.6. Symfony Standard - `composer.json` file

You can use `filefetcher::fetch` to download arbitrary file,
for example `composer.json` in Symfony Standard distribution:

    # Filename: examples/symfony-standard-composer-json.pp
    filefetcher::fetch { 'Symfony Standard composer.json file':
        filename   => 'composer.json',
        target_dir => '/tmp',
        user       => 'vagrant',
        rights     => '755',
        url        => 'https://raw.githubusercontent.com/symfony/symfony-standard/2.7/composer.json',
    }

To run it use the following command:

    sudo puppet apply examples/symfony-standard-composer-json.pp

###3.7. Running phar binaries (`phpunit`, `composer`, etc.)

If php is missing, install it with:

    sudo apt-get install php5 -y

Now, you can use the binaries:

    phpunit --version
    composer --version
    php-cs-fixer --version
    box --version

##6. Reading list

* [Post: Puppet by examples](http://by-examples.net/2015/01/05/puppet-by-examples.html)
* [Puppet by examples](https://github.com/by-examples/puppet-by-examples) on GitHub

## Inspiration

The inspiration came from
[`willdurrand-composer`](https://forge.puppetlabs.com/willdurand/composer)
authored
[by William Durand](https://github.com/willdurand/puppet-composer).
