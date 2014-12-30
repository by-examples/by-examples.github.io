---
layout: post
title: "Symfony/BDD example: red, green, blue"
excerpt: |
    This is the second example in <strong>Symfony/BDD</strong>
    series. It demonstrates how to create simple web pages.
---

##1. Background

I assume that you have already read the first part:
[Symfony/BDD example: Hello, world!](/2014/12/24/bdd-example-hello-world-symfony.html)

The task at hand can be specified as follows:

* I want to create the Symfony application
* the application should consist of three pages:
  - URL: `/`, contents: `Red, red, red`
  - URL: `green.html`, contents: `Green, green, green`
  - URL: `blue.html`, contents: `Blue, blue, blue`

##2. Introductory step

###2.1. Start the project

Create a new directory and clone **my** Symfony Standard repository:

    # Host OS
    $ mkdir symfony-bdd-example-02-red-green-blue
    $ cd symfony-bdd-example-02-red-green-blue
    $ git clone git@github.com:by-examples/symfony-standard.git .

###2.2. Create a starting point for the project

Create a new orphan branch that starts at the tag
Symfony Standard v2.6.1:

    # Host OS
    $ git checkout --orphan 2.6.1/bdd-example-02-red-green-blue v2.6.1

Commit your change:

    # Host OS
    $ git add -A
    $ git commit -m "Symfony Standard 2.6.1"

###2.3. Customize the project

To avoid repeating the same actions again and again
every time I start a new project, I use branches.

To introduce all this changes in your project,
you can cherry pick them:

    # Host OS
    $ git cherry-pick origin/2.6.1/Gitignore
    $ git cherry-pick origin/2.6.1/Cleanup
    $ git cherry-pick origin/2.6.1/Vagrant
    $ git cherry-pick origin/2.6.1/Speedup
    $ git cherry-pick origin/2.6.1/Behat
    $ git cherry-pick origin/2.6.1/BehatInitialization

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

##3. Run Behat - project is GREEN

Now, try to run:

    # Guest OS
    $ bin/behat

You will get the output similar to:

    No scenarios
    No steps
    0m0.03s (9.00Mb)

The project is GREEN.

![GREEN](/img/green.png)

##4. Tests - project is RED

Remove the superflous file `features/homepage.feature`.

Create the file `features/pages.feature` with the
following contents:

    Feature: I want to see three web pages and main menu

      Scenario: Red page
        Given I am on homepage
         Then I should see "Red, red, red"
          And I should see "Main menu"

      Scenario: Green page
        Given I am on "/green.html"
         Then I should see "Green, green, green"
          And I should see "Main menu"

      Scenario: Blue page
        Given I am on "/blue.html"
         Then I should see "Blue, blue, blue"
          And I should see "Main menu"

Run Behat:

    # Guest OS
    $ bin/behat

The project is RED.

![RED](/img/red.png)

Commit the test:

    # Host OS
    $ git add -A
    $ git commit -m "[BDD:RED] tests for three pages: red, green, blue"

##5. Create three pages and menu - project is GREEN

This task consist of:

* creating actions
* creating views
* creating main menu
* cleanning

###5.1. Create actions

Change the contents of the file
`src/AppBundle/Controller/DefaultController.php` as follows:

    //Filename src/AppBundle/Controller/DefaultController.php
    ...
    class DefaultController extends Controller
    {
        /**
         * @Route("/", name="homepage")
         */
        public function redAction()
        {
            return $this->render('default/red.html.twig');
        }

        /**
         * @Route("/green.html", name="green")
         */
        public function greenAction()
        {
            return $this->render('default/green.html.twig');
        }

        /**
         * @Route("/blue.html", name="blue")
         */
        public function blueAction()
        {
            return $this->render('default/blue.html.twig');
        }

    }

###5.2. Create views

Create three files.

The first file `app/Resources/views/default/red.html.twig`:

{% raw %}
    //Filename: app/Resources/views/default/red.html.twig
    {% extends '::base.html.twig' %}

    {% block body %}
        <p>Red, red, red</p>
    {% endblock body %}
{% endraw %}

The second file `app/Resources/views/default/green.html.twig`:

{% raw %}
    //Filename: app/Resources/views/default/green.html.twig
    {% extends '::base.html.twig' %}

    {% block body %}
        <p>Green, green, green</p>
    {% endblock body %}
{% endraw %}

The third file `app/Resources/views/default/blue.html.twig`:

{% raw %}
    //Filename: app/Resources/views/default/blue.html.twig
    {% extends '::base.html.twig' %}

    {% block body %}
        <p>Blue, blue, blue</p>
    {% endblock body %}
{% endraw %}

###5.3. Create main menu

Change the contents of the
`app/Resources/views/base.html.twig` by adding the main menu:

{% raw %}
    //Filename: app/Resources/views/base.html.twig
    ...
    <body>
        <div>
            <h4>Main menu</h4>
            <ul>
                <li><a href="{{ path('homepage') }}">Red</a></li>
                <li><a href="{{ path('green') }}">Green</a></li>
                <li><a href="{{ path('blue') }}">Blue</a></li>
            </ul>
        </div>
        {% block body %}{% endblock %}
        {% block javascripts %}{% endblock %}
    </body>
    ...
{% endraw %}

###5.4. Cleanning

Remove the file `app/Resources/views/default/index.html.twig`.


    # Host OS
    $ rm app/Resources/views/default/index.html.twig


###6. Run the tests


Clear the cache:

    # Guest OS
    $ php app/console cache:clear --env=prod

and run the tests:

    # Guest OS
    $ bin/behat

The project is GREEN.

![GREEN](/img/green.png)

Commit your changes with:

    # Host OS
    $ git add -A
    $ git commit -m "[BDD:GREEN] code for tests: red, green, blue"


##7. Visit app with your browser

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
    $ ab -n 100 http://localhost/green.html

Here are the results I get on my OS X machine:

    Concurrency Level:      1
    Time taken for tests:   1.628 seconds
    Complete requests:      100
    Failed requests:        0
    Non-2xx responses:      100
    Total transferred:      17000 bytes
    HTML transferred:       0 bytes
    Requests per second:    61.42 [#/sec] (mean)
    Time per request:       16.282 [ms] (mean)
    Time per request:       16.282 [ms] (mean, across all concurrent requests)
    Transfer rate:          10.20 [Kbytes/sec] received

My results from Windows machine...

    ...to be done...

##8. Remove unnecessary commits

The history of your project is a linear
and contains 10 commits. The command

    $ git log --oneline

prints the output similar to:

    bb07e3c [BDD:GREEN] code for tests: red, green, blue
    8086044 [BDD:RED] tests for three pages: red, green, blue
    9b35a3a Updated dependencies
    c3466fb [STARTUP] Behat initialization
    716bea7 [STARTUP] Behat installation
    2030974 [STARTUP] Vendor, cache and log in /tmp/symfony2app/
    c957651 [STARTUP] Vagrant
    f523da2 [STARTUP] Remove unnecessary files
    cc41c00 [STARTUP] Gitignore and gitattributes
    41d5d5c Symfony Standard 2.6.1

If you use `--all` switch for `$ git log`, you will see a lot of other
commits (the original commits from Symfony Standard).
To remove them, use:

    $ git remote rm origin
    $ git branch -D 2.7
    $ git tag | xargs git tag -d
    $ git reflog expire --all --expire=now
    $ git prune
    $ git gc

Now your repository contains only 10 commits listed above.

##9. The Example

You will find [the source code of the example
on github](https://github.com/by-examples/symfony-bdd-example-02-red-green-blue).

For the instruction how to run the example refer to `README.md` file.
