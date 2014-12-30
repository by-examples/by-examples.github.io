---
layout: post
title: "Symfony/BDD example: Hello, world!"
excerpt: |
    This example demonstrates <strong>how to begin the adventure
    of BDD</strong>. Here I will show you how to create
    <strong>Hello, world!</strong> application
    using <strong>Symfony</strong> and
    <strong>Behat</strong>.
---

##1. Background

The assumptions are following:

* I want to create the "Hello, world!" application
* working with latest stable release of Symfony
* using Vagrant and `symfony-v0.4.4` Vagrant box

I assume that the command:

    # Host OS
    $ vagrant box list

outputs (among the others):

    symfony-v0.4.4

If it is not the case, you have to start with generating
the box. The complete procedure is available
in a separate post titled:
[Generating symfony-v0.4.4 box](/2014/12/23/generating-symfony-0-4-4-box.html).


<div class="alert alert-info" role="alert">
In this post I am working in two different operating system.
One of them is Host OS, the other is Guest OS.
Host OS is the OS that runs natively on my machine.
Guest OS is the OS that I boot with
<code>$ vagrant up</code> command.

You can switch from Host OS to Guest OS with:
<code>$ vagrant ssh</code> command.

To get back to Host OS from Guest OS use
<code>$ logout</code> or just press
<code>Ctrl+D</code> to close SSH session
</div>


##2. Introductory step

###2.1. Start the project

Create a new directory and clone the Symfony Standard repository:

    # Host OS
    $ mkdir symfony-bdd-example-01-hello-world
    $ cd symfony-bdd-example-01-hello-world
    $ git clone git@github.com:by-examples/symfony-standard.git .

My repository:

    git@github.com:by-examples/symfony-standard.git

contains a Symfony Standard distribution together with
many branches that include various bundles, tools and extensions.

This is the way I work on new examples to avoid repeating the
same actions again and again.

###2.2. Create a starting point for the project

Create a new orphan branch (i.e. the branch,
that has no parent revision). The branch should start
with Symfony Standard v2.6.1:

    # Host OS
    $ git checkout --orphan 2.6.1/bdd-example-01-hello-world v2.6.1

Commit your change:

    # Host OS
    $ git add -A
    $ git commit -m "Symfony Standard 2.6.1"

###2.3. Install Vagrant and Behat

To avoid repeating the same actions again and again
every time I start a new project, I use branches.
I find them useful in almost all my projects.

The branch:

    origin/2.6.1/Gitignore

contains some modifications to `.gitignore` file.
[Here is the list of changes introduced
in origin/2.6.1/Github branch](https://github.com/by-examples/symfony-standard/commit/9ef90bc9b7ab6a63d337576de541d4b2646bfa4f)


The branch:

    origin/2.6.1/Cleanup

removes some unnecessary files.
[Here is the list of changes introduced
in origin/2.6.1/Cleanup branch](https://github.com/by-examples/symfony-standard/commit/b5e4f2485564865f3839d9e977302a4b5844c84d)

Next branch:

    origin/2.6.1/Vagrant

includes Vagrant configuration.
[Here is the list of changes introduced
in origin/2.6.1/Vagrant branch](https://github.com/by-examples/symfony-standard/commit/7336285a310d5195e62934223589b3b5ff84c2d4)

Another branch:

    origin/2.6.1/Speedup

contains improvements that result in substantial speedup of Symfony
application run in Windows/Vagrant environment.
[Here is the list of changes introduced
in origin/2.6.1/Speedup branch](https://github.com/by-examples/symfony-standard/commit/5a81d29b24d3afa392d883aa6664a43a5e08831b)

The last branch:

    origin/2.6.1/Behat

contains Behat installation.
[Here is the list of changes introduced
in origin/2.6.1/Behat branch](https://github.com/by-examples/symfony-standard/commit/aa2a1cc085a06eae0f2340b712431cd8241f6f46)

To introduce all this changes in your project, you can cherry pick them:

    # Host OS
    $ git cherry-pick origin/2.6.1/Gitignore
    $ git cherry-pick origin/2.6.1/Cleanup
    $ git cherry-pick origin/2.6.1/Vagrant
    $ git cherry-pick origin/2.6.1/Speedup
    $ git cherry-pick origin/2.6.1/Behat

##2.4. Install the dependencies

Boot the VM with:

    # Host OS
    $ vagrant up
    $ vagrant ssh

and run:

    # Guest OS
    $ composer install -o

When this is done, commit you changes with:

    # Host OS
    $ git add -A
    $ git commit -m "Updated dependencies"

##3. Initialize Behat

Run the commands:

    # Guest OS
    $ bin/behat  --init

and then change the base class in file
`features/bootstrap/Context/FeatureContex.php`
to `MinkContext`. Do not forget to add use instruction.

Here is the fragment of the file after the change:

    // Filename: features/bootstrap/Context/FeatureContex.php
    [...]
    use Behat\MinkExtension\Context\MinkContext;

    [...]
    class FeatureContext extends MinkContext implements Context, SnippetAcceptingContext
    [...]

Commit your change:

    # Host OS
    $ git add -A
    $ git commit -m "[BEHAT] Initialized and changed base context"

##3. Run Behat - project is GREEN

Now, try to run:

    # Guest OS
    $ bin/behat

You will get the output similar to:

    No scenarios
    No steps
    0m0.03s (7.53Mb)

The project is GREEN.

![GREEN](/img/green.png)

You can print all available step definitions with:

    # Guest OS
    $ bin/behat -dl

##4. The first test - red

Create the file `features/hello-world.feature` with the
following contents:


    Feature: I would like to...

      Scenario: Hello, world page
        Given I am on homepage
         Then I should see "This is my amazing greeting message!"

Run Behat:

    # Guest OS
    $ bin/behat

The project is RED.

![RED](/img/red.png)

Commit the test:

    # Host OS
    $ git add -A
    $ git commit -m "[BDD:RED] homepage test"

##5. Modified view - green

Finish the project by changing the contents
of
`app/Resources/views/default/index.html.twig`.
Add the following message inside `body`:

    <h1>This is my amazing greeting message!</h1>

Clear the cache:

    # Guest OS
    $ php app/console cache:clear --env=prod

and run the test:

    # Guest OS
    $ bin/behat

The project is GREEN.

![GREEN](/img/green.png)

Commit your changes with:

    # Host OS
    $ git add -A
    $ git commit -m "[BDD:GREEN] homepage test"


##5. Visit app with your browser

Run web browser and visit:

    http://localhost:8880/
    http://localhost:8880/app_dev.php/


You can also view your app in guest using lynx:

    # Guest OS
    $ lynx http://localhost/
    $ lynx http://localhost/app_dev.php/


##6. Test the efficiency

You can test the efficiency of the app with:

    # Guest OS
    $ ab -n 100 http://localhost/

Here are the results I get on my OS X machine:

    Concurrency Level:      1
    Time taken for tests:   1.602 seconds
    Complete requests:      100
    Failed requests:        0
    Total transferred:      54700 bytes
    HTML transferred:       28400 bytes
    Requests per second:    62.43 [#/sec] (mean)
    Time per request:       16.017 [ms] (mean)
    Time per request:       16.017 [ms] (mean, across all concurrent requests)
    Transfer rate:          33.35 [Kbytes/sec] received

My results from Windows machine...

    Server Software:        Apache/2.4.10
    Server Hostname:        localhost
    Server Port:            80

    Document Path:          /
    Document Length:        284 bytes

    Concurrency Level:      1
    Time taken for tests:   4.179 seconds
    Complete requests:      100
    Failed requests:        0
    Total transferred:      54700 bytes
    HTML transferred:       28400 bytes
    Requests per second:    23.93 [#/sec] (mean)
    Time per request:       41.791 [ms] (mean)
    Time per request:       41.791 [ms] (mean, across all concurrent requests)
    Transfer rate:          12.78 [Kbytes/sec] received


##7. Remove unnecessary commits

The history of your project is a linear
and contains 10 commits. The command

    $ git log --graph --oneline --decorate

prints the output similar to:

    * c6073c2 (HEAD, 2.6.1/bdd-example-01-hello-world) [BDD:GREEN] homepage test
    * 67c06c0 [BDD:RED] homepage test
    * f0d347e [BEHAT] Initialized and changed base context
    * ffe367d Updated dependencies
    * cc46987 [STARTUP] Behat installation
    * d5879db [STARTUP] Vendor, cache and log in /tmp/symfony2app/
    * 7eccdc0 [STARTUP] Vagrant
    * c314d4e [STARTUP] Remove unnecessary files
    * 00cd6d4 [STARTUP] Gitignore and gitattributes
    * 5e16218 Symfony Standard 2.6.1

If you use `--all` switch for `$ git log`, you will see a lot of other
commits (the original commits from Symfony Standard).
To remove them, use:

    $ git remote rm origin
    $ git branch -D 2.7
    $ git tag | xargs git tag -d
    $ git reflog expire --all --expire=now
    $ git prune
    $ git gc

##8. The Example

You will find [the source code of the example
on github](https://github.com/by-examples/symfony-bdd-example-01-hello-world).

For the instruction how to run the example refer to `README.md` file.
