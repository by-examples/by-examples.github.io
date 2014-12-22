---
layout: post
title: "Generating symfony-v0.4.4 box"
excerpt: |
    In the posts that will follow I will use Vagrant and
    symfony-v0.4.4 box.
    If you want to follow up with the examples,
    you need to prepare and install this box on your machine.
---

##1. Background

I don't like to install binary vagrant boxes.

The main reason for this is that
unknown box can contain malicious software.

The only boxes that I install are the boxes officially
distributed by hashicorp, ubuntu or chef, like:

    ubuntu/trusty32
    ubuntu/trusty64

That's why I prefer to distribute my boxes using their source code.
The term "source code" in this case means:

* Vagrantfile
* automatic provisioning with scripts

This post describes how to prepare and install
the box named symfony-v0.4.4.

##2. Clone the source code

Clone my repository:

    $ git clone --recursive git@github.com:by-examples/symfony-vagrant-box-factory.git
    $ cd symfony-vagrant-box-factory

The version `symfony-v0.4.4` is tagged.
You may see the tags with:

    $ git log --oneline --decorate


##3. Check out the version symfony-v0.4.4

    $ git checkout -b tmp symfony-v0.4.4

Now, before you proceed with booting the box that I have prepared
you may take a look at its contents.

This gives you the opportunity to check wheather this
box contains any software that you consider
dangerous.

In my opinion, everything is safe, but it is up to you
to make this decision.

<div class="alert alert-danger" role="alert">
<strong>
<i class="fa fa-exclamation"></i>
If I had used a binary distribution, you
would not have had the opportunity
to judge the contents of the box.
</strong>
</div>


##4. Generate the box file

    $ vagrant up
    $ vagrant package --output symfony-v0.4.4.box

##5. Install the box in your system

    $ vagrant box add symfony-v0.4.4 symfony-v0.4.4.box

Now, you can remove the box:

    $ rm symfony-v0.4.4.box

##6. Check the installation

The command:

    $ vagrant box list

should now print (probably along with some other boxes) the name:

    symfony-v0.4.4

