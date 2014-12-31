---
layout: post
title: "Symfony/BDD example 06: error 404"
excerpt: |
    This is the final example in the first <strong>Symfony/BDD</strong>
    series. It demonstrates how to create and test error pages.
---

##1. Background

I assume that you have already read:

* [Generating symfony-v0.4.4 box](/2014/12/23/generating-symfony-0-4-4-box.html)
* [Symfony/BDD example 01: Hello, world!](/2014/12/24/bdd-example-hello-world-symfony.html)

In this example we will customize the messages for:

* error 404
* error 500

##2. Introductory step

###2.1. Start the project

Create a new directory and clone **my** Symfony Standard repository:

    # Host OS
    $ mkdir symfony-bdd-example-06-error404
    $ cd symfony-bdd-example-06-error404
    $ git clone git@github.com:by-examples/symfony-standard.git .

###2.2. Create a starting point for the project

Create a new orphan branch that starts at the tag
Symfony Standard v2.6.1:

    # Host OS
    $ git checkout --orphan 2.6.1/symfony-bdd-example-06-error404 v2.6.1

Commit your change:

    # Host OS
    $ git add -A
    $ git commit -m "Symfony Standard 2.6.1"

###2.3. Customize the project

Introduce the following changes in your project:

    # Host OS
    $ git cherry-pick origin/2.6.1/Gitignore
    $ git cherry-pick origin/2.6.1/Cleanup
    $ git cherry-pick origin/2.6.1/Vagrant
    $ git cherry-pick origin/2.6.1/Speedup
    $ git cherry-pick origin/2.6.1/Behat
    $ git cherry-pick origin/2.6.1/BehatInitialization

###2.4. Install the dependencies

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

##4. Error 404 test - the project is RED

Create `features/error.feature` file with the following contents:

    Feature: I would like to...

        Scenario: The Page was not found!
          Given I am on "some/page/that/does/not/exist"
           Then the response status code should be 404
            And I should see "Hey, this beautiful page is yet to be created!"

        Scenario: Serious problem in the application
          Given I am on "/action/with/exception"
           Then the response status code should be 500
            And I should see "We are very sorry, but there was a serious error in the application!"

Run Behat:

    # Guest OS
    $ bin/behat

The project is RED.

![RED](/img/red.png)

Commit the test:

    # Host OS
    $ git add -A
    $ git commit -m "[BDD:RED] tests for error 404 and error 500"


##5. Customized views

Create the file:

    app/Resources/TwigBundle/views/Exception/error404.html.twig

The contents of the file can look like this:

{% raw %}
    {% extends 'base.html.twig' %}

    {% block title %}
        The page does not exists!
    {% endblock title %}

    {% block body %}
        <h2>The page does not exists!</h2>
        <p>
            Hey, this beautiful page is yet to be created!
        </p>
    {% endblock body %}
{% endraw %}

Create the file:

    app/Resources/TwigBundle/views/Exception/error500.html.twig

with the following contents:

{% raw %}
    {% extends 'base.html.twig' %}

    {% block title %}
        An error occured!
    {% endblock title %}

    {% block body %}
        <h2>An error occured!</h2>
        <p>
            We are very sorry, but there was a serious error in the application!
        </p>
    {% endblock body %}
{% endraw %}

Finally, add a new action in `src/AppBundle/Controller/DefaultController.php`:

    /**
     * @Route("/action/with/exception")
     */
    public function actionWithExceptionAction()
    {
        throw new \RuntimeException('Ups...');
    }

##6. Run the tests

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
    $ git commit -m "[BDD:GREEN] code for error 404 and error 500 tests"

##7. Visit app with your browser

Run web browser and visit:

    http://localhost:8880/a/b/c
    http://localhost:8880/app_dev.php/a/b/c

    http://localhost:8880/action/with/exception
    http://localhost:8880/app_dev.php/action/with/exception

##8. Remove unnecessary commits

Remove unnecessary commits from your repository:

    $ git remote rm origin
    $ git branch -D 2.7
    $ git tag | xargs git tag -d
    $ git reflog expire --all --expire=now
    $ git prune
    $ git gc

Now your repository contains only the commits
that you have authored working on this example.

##9. The Example

You will find [the source code of the example
on GitHub](https://github.com/by-examples/symfony-bdd-example-06-error404).

For the instruction how to run the example refer to `README.md` file.

