---
layout: post
title: How to use SSH keys to access Digital Ocean droplet
excerpt: |
    <a href="https://www.digitalocean.com/?refcode=dc864ecee4dc">Digital Ocean</a>
    is a really wonderful place to host your applications.
    In this post I will show you how to generate and configure
    SSH key for your newly created droplet.
---

##1. Generate a new SSH key

Open your shell and create a new ssh key:

    $ ssh-keygen -t rsa -C "developer@example.net" -f developer

You will be asked for a passphrase. To make the things easier
you can use empty passphrase. Just press Enter key twice.

The command will create two files:

    developer
    developer.pub

Move these files to your `.ssh/` directory.

    $ mv developer* ~/.ssh

<div class="alert alert-danger" role="alert">
<strong>
<i class="fa fa-exclamation"></i>
If your <code>.ssh/</code> directory does not exist, you can
create it with:
</strong>
<pre><code>
$ cd
$ mkdir .ssh
</code></pre>
</div>


##2. Add the SSH key to your Digital Ocean account

Log in to your  <a href="https://www.digitalocean.com/?refcode=dc864ecee4dc">Digital Ocean</a>
account and upload the key
stored in `developer.pub` using SSH keys/Add SSH Key
button.

<p class="figure">
    <img src="/img/2014-12-15/01.png">
</p>

##3. Create a new droplet

During creation of a new droplet remember
about adding your `developer` key to the
list of authorized keys.

<p class="figure">
    <img src="/img/2014-12-15/02.png">
</p>

##4. Update your .ssh/config

Open your SSH configuration file with vi:

    $ vi ~/.ssh/config

<div class="alert alert-danger" role="alert">
<strong>
<i class="fa fa-exclamation"></i>
If your <code>.ssh/config</code> file does not exist, you can
create it with <code>$ touch .ssh/config</code>.
</strong>
</div>

Then enter the configuration for your droplet
hosted by <a href="https://www.digitalocean.com/?refcode=dc864ecee4dc">Digital Ocean</a>:

    Host DigitalOcean
      HostName 178.62.114.201
      User root
      IdentityFile  ~/.ssh/developer

The IP comes from your droplet properties page.

<p class="figure">
    <img src="/img/2014-12-15/03.png">
</p>

##5. Verify your ssh access

Run the command:

    $ ssh DigitalOcean

It should start a new ssh session to your droplet.

