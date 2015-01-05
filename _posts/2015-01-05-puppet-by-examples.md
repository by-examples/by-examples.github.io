---
layout: post
title: "Puppet by examples"
excerpt: |
    Today I spent most of the day collecting various examples
    concerning puppet. The goal I have in mind is to have
    a single place to test all the Puppet stuff.

---

<div class="alert alert-success text-center">
<strong>
<i class="fa fa-thumbs-o-up"></i>
You will find
<a href="https://github.com/by-examples/puppet-by-examples">the source code of the examples on GitHub</a>
</strong>
</div>

##1. Introduction

Puppet is a well-known **software configuration utility**.
I use it to prepare Vagrant boxes.
It can be seen as an abstraction layer,
that helps one to define resources in a platform independent way.

A resource can be almost anything that you deal with when you set up
a new server or reconfigure an existing one. It can be:

* a file (e.g. `/etc/hosts`)
* a single line in a file
* a deamon (e.g. `httpd`, `sshd`)
* a software package (e.g. `git`, `mc`, `lynx`)
* a command (e.g. `make ...`, `wget ...`)
* etc.

If you don't know any software configuration solution
Puppet is definitely worth trying.

<div class="alert alert-info">
<strong>
Chef and Ansible are the most well known alternatives to Puppet.
For more comprehensive list refer to
<a href="http://en.wikipedia.org/wiki/Comparison_of_open-source_configuration_management_software">
Comparison of open-source configuration management software
</a> Wikipedia entry.
</strong>
</div>

##2. First step: resources

The first step to master Puppet is to get acquainted with
resources.

The complete documentation for resources is available
on [Puppet's site](https://docs.puppetlabs.com/references/latest/type.html)

##3. How to use the examples?

The examples I prepared are mostly trivial and self documented.
To run them, one by one, create a local clone:

    $ git clone git@github.com:by-examples/puppet-by-examples.git
    $ cd puppet-by-examples

Then you need to boot the VM.
You can change the system within `puppet-by-examples/Vagrantfile`:

* Ubuntu 12.04
* Ubuntu 14.04
* CentOS 6.5
* CentOS 7.0

The instruction that follows was written for Ubuntu 14.04
(this is the default setting).

Run:

    # Host OS
    $ vagrant up
    $ vagrant ssh

Once in guest, change the current directory to `/vagrant`:

    # Guest OS
    $ cd /vagrant

Now, you can run the Puppet examples that I have prepared.

The command:

    $ puppet apply 01-resources/notify/notify-1.pp

will execute the example `notify-1.pp`.

To learn basics of Puppet:

* run the example
* take a look at the source code

##4. The Examples

I suggest you to work in the following order:

* notify (directory `01-resources/notify/`)
* file (directory `01-resources/file/`)
* array (directory `01-resources/array/`)
* package (directory `01-resources/package/`)
* define (directory `01-resources/define/`)
* download-file (directory `01-resources/download-file/`)
* ordering (directory `01-resources/ordering/`)
* stdlib (directory `01-resources/stdlib/`)
* well-known-modules (directory `01-resources/well-known-modules/`)

Some of the examples need root privileges and additional software.

Take a look at the example
`01-resources/well-known-modules/puppetlabs-apache-1.pp`.
You will find in this example the following comment:

    # Ubuntu
    #   sudo puppet module install puppetlabs-apache --version 1.2.0
    #   sudo puppet apply puppetlabs-apache-1.pp
    #   sudo apt-get install lynx-cur -y
    #   lynx by-examples.lh

It means, that in order to run the example in Ubuntu,
you have to execute the following commands:

    # Guest OS
    $ sudo puppet module install puppetlabs-apache --version 1.2.0
    $ sudo puppet apply puppetlabs-apache-1.pp
    $ sudo apt-get install lynx-cur -y
    $ lynx by-examples.lh

The command:

    $ sudo puppet apply puppetlabs-apache-1.pp

will work only in
`01-resources/well-known-modules/` directory.
You have to proceed it with:

    cd 01-resources/well-known-modules/

##5. The Examples

You will find [the source code of the example
on GitHub](https://github.com/by-examples/symfony-bdd-app-01-rivers).

##6. Reading list

* [Wikipedia: Comparison of open-source configuration management software](http://en.wikipedia.org/wiki/Comparison_of_open-source_configuration_management_software)
* [Wikipedia: Puppet](http://en.wikipedia.org/wiki/Puppet_%28software%29)