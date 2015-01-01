---
layout: post
title: "Symfony/BDD example 04: login/logout"
excerpt: |
    This is the fourth example in <strong>Symfony/BDD</strong>
    series. It demonstrates how to use FOSUserBundle.
---

##1. Background

I assume that you have already read:

* [Symfony/BDD example 01: Hello, world!](/2014/12/24/bdd-example-hello-world-symfony.html)
* [Symfony/BDD example 02: red, green, blue](/2014/12/30/bdd-example-red-green-blue.html)
* [Symfony/BDD example 03: mountains](/2014/12/31/bdd-example-mountains.html)

In this example I want to restrict access to the application.

The access is to be granted only to one user:

    username:  admin
    password:  loremipsum

##2. Introductory step

###2.1. Start the project

Create a new directory and clone **my** Symfony Standard repository:

    # Host OS
    $ mkdir symfony-bdd-example-04-login-logout
    $ cd symfony-bdd-example-04-login-logout
    $ git clone git@github.com:by-examples/symfony-standard.git .

###2.2. Create a starting point for the project

Create a new orphan branch that starts at the tag
Symfony Standard v2.6.1:

    # Host OS
    $ git checkout --orphan 2.6.1/symfony-bdd-example-04-login-logout v2.6.1

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
    $ git cherry-pick origin/2.6.1/Db
    $ git cherry-pick origin/2.6.1/FOSUserBundle

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

##5. Tests - the project is RED

Create the file `features/login.feature` with the
following contents:

    Feature: I would like to log in to the system

      Scenario: Log in as admin
        Given I am on homepage
         Then I should not see "Logged in as admin"
         When I follow "Login"
          And I fill in "username" with "admin"
          And I fill in "password" with "loremipsum"
          And I press "Login"
         Then I should see "Logged in as admin"
         Then I should not see "Login"
         When I follow "Logout"
         Then I should not see "Logged in as admin"
         Then I should see "Login"

      Scenario: Unsuccessful login
        Given I am on homepage
         When I follow "Login"
         When I fill in "username" with "wrong username"
          And I fill in "password" with "wrong password"
          And I press "Login"
         Then I should see "Bad credentials"

      Scenario: Profile unavailable
        Given I go to "/profile"
         Then the response status code should be 404

      Scenario: Resetting unavailable
        Given I go to "/resetting"
         Then the response status code should be 404


Run Behat:

    # Guest OS
    $ bin/behat

The project is RED.

![RED](/img/red.png)

Commit the test:

    # Host OS
    $ git add -A
    $ git commit -m "[BDD:RED] tests for login/logout"


##6. Restrict access to the application

Restrict access to the whole application
in `app/config/security.yml` file:

    ...

    access_control:
        - { path: ^/_wdt, roles: 'IS_AUTHENTICATED_ANONYMOUSLY' }
        #- { path: ^/login, roles: IS_AUTHENTICATED_ANONYMOUSLY, requires_channel: https }
        - { path: ^/login$, role: IS_AUTHENTICATED_ANONYMOUSLY }
        #- { path: ^/register, role: IS_AUTHENTICATED_ANONYMOUSLY }
        #- { path: ^/resetting, role: IS_AUTHENTICATED_ANONYMOUSLY }
        #- { path: ^/admin/, role: ROLE_ADMIN }
        - { path: ^/, role: ROLE_SUPER_ADMIN }

    ...


Commit your changes with:

    # Host OS
    $ git add -A
    $ git commit -m "[SECURITY] restricted access to /"


##7. The Database

Create the database:

    # Guest OS
    $ php app/console doctrine:schema:update --force

##8. The admin's account

Crete the admin account:

    # Guest OS
    $ php app/console fos:user:create admin admin@example.net loremipsum --super-admin

or:

    # Guest OS
    $ php app/console fos:user:create
    $ php app/console fos:user:promote --super admin


Now you can visit your website:

    http://127.0.0.1:8880/app_dev.php/

and log in as:

    user:     admin
    password: loremipsum


##9. The main template

Add the following content to your
`app/Resources/views/base.html.twig` view:

{% raw %}
    ...
    <body>
        <ul>
            {% if is_granted('ROLE_USER') %}
                <li>Logged in as {{ app.user.username }}</li>
                <li>
                    <a href="{{ path('fos_user_security_logout') }}">
                        {{ 'layout.logout'|trans({}, 'FOSUserBundle') }}
                    </a>
                </li>
            {% else %}
                <li>
                    <a href="{{ path('fos_user_security_login') }}">
                        {{ 'layout.login'|trans({}, 'FOSUserBundle') }}
                    </a>
                </li>
            {% endif %}
        </ul>

        {% block body %}{% endblock %}
        ...
{% endraw %}

##10. Run the tests

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
    $ git commit -m "[BDD:GREEN] code for test: login/logout"

##11. Visit app with your browser

Run web browser and visit:

    http://localhost:8880/
    http://localhost:8880/app_dev.php/


##12. Remove unnecessary commits

Remove unnecessary commits from your repository:

    $ git remote rm origin
    $ git branch -D 2.7
    $ git tag | xargs git tag -d
    $ git reflog expire --all --expire=now
    $ git prune
    $ git gc

Now your repository contains only the commits
that you have authored working on this example.

##13. The Example

You will find [the source code of the example
on GitHub](https://github.com/by-examples/symfony-bdd-example-04-login-logout).

For the instruction how to run the example refer to `README.md` file.
