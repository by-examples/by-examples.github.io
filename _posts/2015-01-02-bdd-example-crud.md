---
layout: post
title: "Symfony/BDD example 05: CRUD"
excerpt: |
    This is the fifth example in <strong>Symfony/BDD</strong>
    series. It demonstrates how to create and test CRUD.
---

##1. Background

I assume that you have already read:

* [Symfony/BDD example 01: Hello, world!](/2014/12/24/bdd-example-hello-world-symfony.html)
* [Symfony/BDD example 02: red, green, blue](/2014/12/30/bdd-example-red-green-blue.html)
* [Symfony/BDD example 03: mountains](/2014/12/31/bdd-example-mountains.html)
* [Symfony/BDD example 04: login/logout](/2015/01/01/bdd-example-login-logout.html)

In this example I want to create
an administration panel to enter and modify records in the database.

The database will contain a table `River` to store
some information about rivers.
Every record will have the following columns:

* `name` (string 255)
* `length` (integer)


##2. Introductory step

###2.1. Start the project

Create a new directory and clone **my** Symfony Standard repository:

    # Host OS
    $ mkdir symfony-bdd-example-05-crud
    $ cd symfony-bdd-example-05-crud
    $ git clone git@github.com:by-examples/symfony-standard.git .

###2.2. Create a starting point for the project

Create a new orphan branch that starts at the tag
Symfony Standard v2.6.1:

    # Host OS
    $ git checkout --orphan 2.6.1/symfony-bdd-example-05-crud v2.6.1

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

##4. CRUD Tests - the project is RED

Create the file `features/crud.feature` with the
following contents:

    Feature: I would like to edit rivers

      Scenario Outline: Insert records
       Given I am on "/admin/river"
        Then I should not see "<river>"
         And I follow "Create a new entry"
        Then I should see "River creation"
        When I fill in "Name" with "<river>"
         And I fill in "Length" with "<length>"
         And I press "Create"
        Then I should see "<river>"
         And I should see "<length>"

      Examples:
        | river          | length |
        | ABC RIV        | 7182   |
        | Vistula RIV    | 1234   |
        | The Thames RIV | 555    |


      Scenario Outline: Edit records
       Given I am on "/admin/river"
        Then I should not see "<new-river>"
        When I follow "<old-river>"
        Then I should see "<old-river>"
        When I follow "Edit"
         And I fill in "Name" with "<new-river>"
         And I fill in "Length" with "<new-length>"
         And I press "Update"
         And I follow "Back to the list"
        Then I should see "<new-river>"
         And I should see "<new-length>"
         And I should not see "<old-river>"

      Examples:
        | old-river     | new-river       | new-length |
        | Vistula RIV   | VI-stula RIV    | 9876       |
        | ABC RIV       | The NEW RIV     | 3333       |


      Scenario Outline: Delete records
       Given I am on "/admin/river"
        Then I should see "<river>"
        When I follow "<river>"
        Then I should see "<river>"
        When I press "Delete"
        Then I should not see "<river>"

      Examples:
        |  river         |
        | VI-stula RIV   |
        | The NEW RIV    |
        | The Thames RIV |

Run Behat:

    # Guest OS
    $ bin/behat

The project is RED.

![RED](/img/red.png)

Commit the test:

    # Host OS
    $ git add -A
    $ git commit -m "[BDD:RED] tests for CRUD"

##5. Generate River entity

Run the command:

    $ php app/console doctrine:generate:entity

In this example the entity's name is:

    AppBundle:River

Leave default settings for format: `annotation`
and add two columns:

    name     -   string 255
    length   -   integer

When you are finished press enter (as many times as necessary).

The command generates a class stored in:

    src/AppBundle/Entity/River.php

For this entity the ORM will create
a table named `River`.

Commit your changes with:

    $ git commit -m "[BDD:RED] Create River entity"


##6. The Database

Now that we have an entity class we want to create a table in the database:

    # Guest OS (Linux/Ubuntu)
    $ php app/console doctrine:schema:update --force

##7. Generate CRUD

Generate CRUD:

    # Guest OS (Linux/Ubuntu)
    $ php app/console doctrine:generate:crud

You will be asked a number of questions.
The three of them are crucial for us:

* Entity name: `AppBundle:River`
* With write actions: `yes`
* Routes prefix: `/admin/river`

Commit your changes with:

    $ git commit -m "[BDD:RED] CRUD for River entity"


##8. Customize the list of records

Change the file `src/AppBundle/Resources/views/River/index.html.twig`:

{% raw %}
    ...
    <tbody>
    {% for entity in entities %}
        <tr>
            <td>{{ loop.index }}.</td>
            <td>
                <a href="{{ path('admin_river_show', { 'id': entity.id }) }}">
                    {{ entity.name }}
                </a>
            </td>
            <td>{{ entity.length }}</td>
            <td>
    ...
{% endraw %}

##9. Run the tests

Clear the cache:

    # Guest OS
    $ php app/console cache:clear --env=prod

Rebuild the database:

    # Guest OS
    $ mysql -u root < 00-extra/db/create-empty-database.sql
    $ php app/console doctrine:schema:update --force

and run the tests:

    # Guest OS
    $ bin/behat


The project is GREEN.

![GREEN](/img/green.png)

Commit your changes with:

    # Host OS
    $ git add -A
    $ git commit -m "[BDD:GREEN] code for CRUD tests"

##10. Visit app with your browser

Run web browser and visit:

    http://localhost:8880/admin/river
    http://localhost:8880/app_dev.php/admin/river

##11. Remove unnecessary commits

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
on GitHub](https://github.com/by-examples/symfony-bdd-example-05-crud).

For the instruction how to run the example refer to `README.md` file.
