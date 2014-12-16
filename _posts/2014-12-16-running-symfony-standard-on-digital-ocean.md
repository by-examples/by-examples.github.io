---
layout: post
title: Running Symfony Standard on Digital Ocean
excerpt: |
    Here you will find the explanation how to run
    Symfony Standard app
    <a href="https://www.digitalocean.com/?refcode=dc864ecee4dc">Digital Ocean</a>.
---

##1. Background

Once you start using Symfony framework sooner or later
you will need to publish your work.
<a href="https://www.digitalocean.com/?refcode=dc864ecee4dc">Digital Ocean</a>
is a perfect hosting solution.
In this tutorial I will show you how to run generic Symfony Standard
on <a href="https://www.digitalocean.com/?refcode=dc864ecee4dc">Digital Ocean</a>.

The assumption is that you
<a href="/2014/12/15/using-ssh-key-for-digital-ocean.html">
How to use SSH keys to access Digital Ocean droplet
</a>
already.

##2. Create a new droplet

Log in to you
<a href="https://www.digitalocean.com/?refcode=dc864ecee4dc">Digital Ocean</a>
account, press **Create** button and select
the droplet LAMP on Ubuntu 14.04.

<p class="figure">
    <img src="/img/2014-12-16/01.png">
</p>

<div class="alert alert-danger" role="alert">
<strong>
<i class="fa fa-exclamation"></i>
Remember to add your <code>developer</code>
SSH key for this droplet.
The procedure to generate and configure the key
is described in
the post titled:
<a href="/2014/12/15/using-ssh-key-for-digital-ocean.html">
How to use SSH keys to access Digital Ocean droplet
</a>
</strong>
</div>

##3. Check ssh access

Verify that your ssh key was configured correctly.
Run the command:

    $ ssh DigitalOcean

It should start ssh session to your newly created droplet.

##4. Install missing software

The default droplet configuration misses
the following software that we will need:

* git
* php5-curl
* apache module mod_rewrite
* composer

To install it, run the following commands
(they all should be executed inside ssh session to your droplet):

    $ apt-get update -y

    $ apt-get install git -y
    $ apt-get install php5-curl -y
    $ a2enmod rewrite

    $ cd
    $ curl -sS https://getcomposer.org/installer | php
    $ mv composer.phar /usr/local/bin/composer

<div class="alert alert-danger" role="alert">
<strong>
<i class="fa fa-exclamation"></i>
The above commands assume that you are logged in with root's privileges.
If not, add <code>sudo</code> before each of them.
</strong>
</div>

##5. Clone Symfony Standard

Using ssh session to your droplet clone the Symfony Standard
distribution to `/apps/my-new-app` directory:

    $ mkdir /apps
    $ cd /apps
    $ mkdir my-new-app
    $ cd my-new-app
    $ git clone https://github.com/symfony/symfony-standard.git .

I will use the default application included in v2.3.23
distribution. This is a single webpage with the
message **Homepage**.

Run the command:

    $ git checkout -b the-app v2.3.23

You will have a branch named `the-app` with the
Symfony Standard v2.3.23.

Now we need to install dependencies.

##6. Install dependencies

The commands shown below should also be executed inside your droplet's
ssh console:

    $ cd /apps/my-new-app
    $ composer install

You will be asked a couple of questions:

    database_driver (pdo_mysql):
    database_host (127.0.0.1):
    database_port (null):
    database_name (symfony):
    database_user (root):
    database_password (null):
    mailer_transport (smtp):
    mailer_host (127.0.0.1):
    mailer_user (null):
    mailer_password (null):
    locale (en):
    secret (ThisTokenIsNotSoSecretChangeIt):

Use default values for all of them. Just press enter as many
times as you are asked anything.

Now we can publish the project.
To do this we need to change file ownership and reconfigure Apache.

##7. Change the owner

Change the owner of all files stored within `/apps` directory
to Apache user (e.g. `www-data`):

    $ chown -R www-data:www-data /apps

##8. Reconfigure Apache's public html dir

By default, Apache publishes the contents of `/var/www/html` directory.
We will replace it with a symbolic link to our project.

Remove `/var/www/html` directory:

    $ rm -rf /var/www/html

Create a new symbolic link

    $ ln -s /apps/my-new-app/web /var/www/html

Change the owner of the symbolic link to `www-data`:

    $ chown www-data:www-data /var/www/html

##9. Reconfigure Apache

Edit the file `/etc/apache2/apache2.conf`:

    $ vi /etc/apache2/apache2.conf

and replace:

    <Directory /var/www/>
            Options Indexes FollowSymLinks
            AllowOverride None          <=== this is to be changed
            Require all granted
    </Directory>

with:

    <Directory /var/www/>
            Options Indexes FollowSymLinks
            AllowOverride All          <=== new valued here
            Require all granted
    </Directory>

The above change turns on `.htaccess` files in
`/var/www` directory
and its subdirectories.

##10. Restart the Apache

Run either of the two commands:

    $ /etc/init.d/apache2 restart

    $ service apache2 restart

##11. Visit your app

Start your browser and visit:

    http://178.62.114.201

You should see:

    Homepage.

The IP comes from your droplet configuration.

<p class="figure">
    <img src="/img/2014-12-15/03.png">
</p>

