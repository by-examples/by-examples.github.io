---
layout: post
title: "Symfony/BDD/Behat/Tutorial: Application 01: Rivers"
excerpt: |
    This is the tutorial that explains how to get started with:
    Symfony and Behat.
    It contains a step by step procedure to develop
    a simple web application in a BDD style.

---

<div class="alert alert-success text-center">
<strong>
<i class="fa fa-thumbs-o-up"></i>
You will find
<a href="https://github.com/by-examples/symfony-bdd-app-01-rivers">the source code of the example on GitHub</a>
</strong>
</div>



##1. Introduction

###1.1. Background

I assume that you have already read (this is a compulsory reading):

* [Generating symfony-v0.4.4 box](/2014/12/23/generating-symfony-0-4-4-box.html)

You may find the following introductory posts interesting as they present
smaller chunks of this tutorial:

* [Symfony/BDD example 01: Hello, world!](/2014/12/24/bdd-example-hello-world-symfony.html)
* [Symfony/BDD example 02: red, green, blue](/2014/12/30/bdd-example-red-green-blue.html)
* [Symfony/BDD example 03: mountains](/2014/12/31/bdd-example-mountains.html)
* [Symfony/BDD example 04: login/logout](/2015/01/01/bdd-example-login-logout.html)
* [Symfony/BDD example 05: CRUD](/2015/01/02/bdd-example-crud.html)
* [Symfony/BDD example 06: error 404](/2015/01/03/bdd-example-error404.html)

###1.2. The specification of the application

1. The application to store rivers in the database.
2. Every river has two attributes: name and length.
3. Homepage should present an HTML table containing all the rivers
   from the database.
4. Fixtures
    - load fixtures from YML file
    - show all the records from DB as an HTML table on homepage
5. Access
    - Everyone can access the list of all the rivers in read mode.
    - Admin can access the list of all rivers in read/write mode through CRUD administration panel.
6. Login/logout
    - login ,logged as X, logout -  links/texts on homepage
    - homepage, panel admin - links on homepage
    - registration, resetting and profile - unvavailable
7. The admin panel: CRUD
    - /admin/river/ - the administration panel available to logged in users
8. Customized 404, 500 errors
9. One user account available by default
    - username: admin
    - password: loremipsum

##2. Introductory step

###2.1. Start the project

Create a new directory and clone **my** Symfony Standard repository:

    # Host OS
    $ mkdir symfony-bdd-app-01-rivers
    $ cd symfony-bdd-app-01-rivers
    $ git clone git@github.com:by-examples/symfony-standard.git .

###2.2. Create a starting point for the project

Create a new orphan branch that starts at the tag
Symfony Standard v2.6.1:

    # Host OS
    $ git checkout --orphan 2.6.1/symfony-bdd-app-01-rivers v2.6.1

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
    $ git cherry-pick origin/2.6.1/DoctrineFixturesBundle
    $ git cherry-pick origin/2.6.1/FOSUserBundle
    $ git cherry-pick origin/2.6.1/Error404
    $ git cherry-pick origin/2.6.1/Travis
    $ git cherry-pick origin/2.6.1/Scripts

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

##4. Homepage

###4.1. The feature file

Filename: `homepage.feature`

    Feature: I would like to...

        Scenario: Homepage should be accessible
          Given I am on homepage
           Then I should see "The Longest Rivers in the World!"

[The Changeset on GitHub](https://github.com/by-examples/symfony-bdd-app-01-rivers/commit/2802521e2995af0a705cba4548af7b2e8a6e70f1)

###4.2. Behat - RED

Run Behat:

    # Guest OS
    $ php app/console cache:clear --env=prod
    $ bin/behat

The project is RED.

![RED](/img/red.png)

###4.3. The commit - RED

Commit you changes with:

    # Host OS
    $ git add -A
    $ git commit -m "[RED] Homepage: test"

###4.4. The code

Filename: `app/Resources/views/base.html.twig`:

{% raw %}
    ...
    <body>
        <h1>The Longest Rivers in the World!</h1>
        {% block body %}{% endblock %}
    ...
{% endraw %}

[The Changeset on GitHub](https://github.com/by-examples/symfony-bdd-app-01-rivers/commit/1603bb76d977e7e685f08e93b8e5ad4ca7d693a4)

###4.5. Behat - GREEN

Run Behat:

    # Guest OS
    $ php app/console cache:clear --env=prod
    $ bin/behat

The project is GREEN.

![GREEN](/img/green.png)

###4.6. The commit - GREEN

Commit you changes with:

    # Host OS
    $ git add -A
    $ git commit -m "[GREEN] Homepage: code"

###5. Error pages

###5.1. The feature file

Filename: `features/error.feature`:

    Feature: I would like to see customized error pages

        Scenario: The Page was not found!
          Given I am on "some/page/that/does/not/exist"
           Then the response status code should be 404
            And I should see "Hey, this beautiful page is yet to be created!"

        Scenario: Serious problem in the application
          Given I am on "/action/with/exception"
           Then the response status code should be 500
            And I should see "We are very sorry, but there was a serious error in the application!"

[The Changeset on GitHub](https://github.com/by-examples/symfony-bdd-app-01-rivers/commit/e12352ea85e40ac3964dc2277855b2ca46a4cc9a)

###5.2. Behat - RED

Run Behat:

    # Guest OS
    $ php app/console cache:clear --env=prod
    $ bin/behat

The project is RED.

![RED](/img/red.png)

###5.3. The commit - RED

Commit you changes with:

    # Host OS
    $ git add -A
    $ git commit -m "[RED] Error pages: test"

###5.4. The code

Filename: `app/Resources/TwigBundle/views/Exception/error404.html.twig`:

{% raw %}
    ...
    {% block body %}
        <h2>The page does not exists!</h2>
        <p>
            Hey, this beautiful page is yet to be created!
        </p>
    {% endblock body %}
    ...
{% endraw %}

Filename: `app/Resources/TwigBundle/views/Exception/error500.html.twig`:

{% raw %}
    ...
    {% block body %}
        <h2>An error occured!</h2>
        <p>
            We are very sorry, but there was a serious error in the application!
        </p>
    {% endblock body %}
    ...
{% endraw %}

Filename: `src/AppBundle/Controller/DefaultController.php`:

    ...
    /**
     * @Route("/action/with/exception")
     */
    public function actionWithExceptionAction()
    {
        throw new \RuntimeException('Ups...');
    }
    ...

[The Changeset on GitHub](https://github.com/by-examples/symfony-bdd-app-01-rivers/commit/84ee55b4414635c16135731686bfd00783dd19c3)

###5.5. Behat - GREEN

Run Behat:

    # Guest OS
    $ php app/console cache:clear --env=prod
    $ bin/behat

The project is GREEN.

![GREEN](/img/green.png)

###5.6. The commit - GREEN

Commit you changes with:

    # Host OS
    $ git add -A
    $ git commit -m "[GREEN] Error pages: code"

###6. Login/logout

###6.1. The feature file

Filename: `features/login.feature`:

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

[The Changeset on GitHub](https://github.com/by-examples/symfony-bdd-app-01-rivers/commit/01db77682a4ab30968393974fc4e44f51a3b380b)

###6.2. Behat - RED

Run Behat:

    # Guest OS
    $ php app/console cache:clear --env=prod
    $ bin/behat

The project is RED.

![RED](/img/red.png)

###6.3. The commit - RED

Commit you changes with:

    # Host OS
    $ git add -A
    $ git commit -m "[RED] Login/logout: test"

###6.4. The code

Create the database and admin's account:

    # Guest OS
    $ php app/console doctrine:schema:update --force
    $ php app/console fos:user:create admin admin@example.net loremipsum --super-admin

Filename: `app/Resources/views/base.html.twig`:

{% raw %}
    ...
    <h1>The Longest Rivers in the World!</h1>
    {% block login_logout_panel %}
        <ul>
            <li>
                <a href="{{ path('homepage') }}">
                    Homepage
                </a>
            </li>
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
    {% endblock login_logout_panel %}
    {% block body %}{% endblock %}
    ...
{% endraw %}

Filename: `app/Resources/TwigBundle/views/Exception/error404.html.twig`:

{% raw %}
    ...
    {% endblock title %}

    {% block login_logout_panel %}
    {% endblock login_logout_panel %}

    {% block body %}
    ...
{% endraw %}

Filename: `app/Resources/TwigBundle/views/Exception/error500.html.twig`:

{% raw %}
    ...
    {% endblock title %}

    {% block login_logout_panel %}
    {% endblock login_logout_panel %}

    {% block body %}
    ...
{% endraw %}

[The Changeset on GitHub](https://github.com/by-examples/symfony-bdd-app-01-rivers/commit/c4495ec70e7a2e783cd028758406e3b97fcf5382)

###6.5. Behat - GREEN

Run Behat:

    # Guest OS
    $ php app/console cache:clear --env=prod
    $ bin/behat

The project is GREEN.

![GREEN](/img/green.png)

###6.6. The commit - GREEN

Commit you changes with:

    # Host OS
    $ git add -A
    $ git commit -m "[GREEN] Login/logout: code"

###7. CRUD

###7.1. The feature file

Filename: `features/crud.feature`:

    Feature: I would like to edit rivers

      Scenario Outline: Insert records
       Given I am on homepage
         And I follow "Login"
         And I fill in "Username" with "admin"
         And I fill in "Password" with "loremipsum"
         And I press "Login"
         And I go to "/admin/river/"
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
       Given I am on homepage
         And I follow "Login"
         And I fill in "Username" with "admin"
         And I fill in "Password" with "loremipsum"
         And I press "Login"
         And I go to "/admin/river/"
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
       Given I am on homepage
         And I follow "Login"
         And I fill in "Username" with "admin"
         And I fill in "Password" with "loremipsum"
         And I press "Login"
         And I go to "/admin/river/"
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

[The Changeset on GitHub](https://github.com/by-examples/symfony-bdd-app-01-rivers/commit/de1d51ee1eacc309f9ffa3589872e7380d8da00b)

###7.2. Behat - RED

Run Behat:

    # Guest OS
    $ php app/console cache:clear --env=prod
    $ bin/behat

The project is RED.

![RED](/img/red.png)

###7.3. The commit - RED

Commit you changes with:

    # Host OS
    $ git add -A
    $ git commit -m "[RED] River CRUD: test"

###7.4. The code

####7.4.1. Generate River entity

Run the command:

    $ php app/console doctrine:generate:entity

The entity's name is:

    AppBundle:River

Leave default settings for format: `annotation`
and add two columns:

    name     -   string 255
    length   -   integer

When you are finished press enter (as many times as necessary).

[The Changeset on GitHub](https://github.com/by-examples/symfony-bdd-app-01-rivers/commit/50af1f103573500ad560f2d814f5f9ad571228b9)

####7.4.2. Commit

Commit your changes with:

    # Host OS
    $ git add -A
    $ git commit -m "[RED] Create River entity"

##7.4.3. The Database

Update the database:

    # Guest OS
    $ php app/console doctrine:schema:update --force

##7.4.4. CRUD

Generate CRUD:

    # Guest OS
    $ php app/console doctrine:generate:crud

You will be asked a number of questions.
The three of them are crucial for us:

* Entity name: `AppBundle:River`
* With write actions: `yes`
* Routes prefix: `/admin/river`

[The Changeset on GitHub](https://github.com/by-examples/symfony-bdd-app-01-rivers/commit/4aa8f12be6c0afe96fe87ac1883274591a6a2548)

####7.4.5. Commit

Commit your changes with:

    # Host OS
    $ git add -A
    $ git commit -m "[RED] Default CRUD for River entity"

####7.4.6. Customize the list of records

Filename: `src/AppBundle/Resources/views/River/index.html.twig`:

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

[The Changeset on GitHub](https://github.com/by-examples/symfony-bdd-app-01-rivers/commit/92031180aa29c1d4502c5e9e377128bdf9c000ca)

###7.5. Behat - GREEN

Run Behat:

    # Guest OS
    $ php app/console cache:clear --env=prod
    $ bin/behat

The project is GREEN.

![GREEN](/img/green.png)

###7.6. The commit - GREEN

Commit you changes with:

    # Host OS
    $ git add -A
    $ git commit -m "[GREEN] River CRUD: customized view for index action"

###8. Fixtures

###8.1. The feature file

Filename: `features/fixtures.feature`:

    Feature: We want one page with all the rivers loaded from YML file

      Scenario: List mountains
        Given I am on homepage
         Then I should see "The Nile"
          And I should see "1234"
         Then I should see "The Thames"
          And I should see "9876"
         Then I should see "Mississipi"
          And I should see "3434"

      Scenario: I want to check the number of records
        When I am on homepage
        Then I should see 3 "table tbody tr" elements

[The Changeset on GitHub](https://github.com/by-examples/symfony-bdd-app-01-rivers/commit/7cd25b64d86fe01fbbc5a83df9d29b93db47757f)

###8.2. Behat - RED

Run Behat:

    # Guest OS
    $ php app/console cache:clear --env=prod
    $ bin/behat

The project is RED.

![RED](/img/red.png)

###8.3. The commit - RED

Commit you changes with:

    # Host OS
    $ git add -A
    $ git commit -m "[RED] Fixtures: test"

###8.4. The code

###8.4.1. YML file

Filename: `data/rivers.yml`:

    - { name: "The Nile", length: 1234 }
    - { name: "The Thames", length: 9876 }
    - { name: "Mississipi", length: 3434 }

###8.4.2. The code to load fixtures

Filename: `src/AppBundle/DataFixtures/ORM/LoadRivers.php`:

    <?php

    namespace AppBundle\DataFixtures\ORM;

    use Doctrine\Common\DataFixtures\FixtureInterface;
    use Doctrine\Common\Persistence\ObjectManager;

    use Symfony\Component\Yaml\Yaml;

    use AppBundle\Entity\River;

    class LoadRivers implements FixtureInterface
    {
        /**
         * {@inheritDoc}
         */
        public function load(ObjectManager $manager)
        {

            $filename = __DIR__ . '/../../../../data/rivers.yml';
            $yml = Yaml::parse(file_get_contents($filename));
            foreach ($yml as $item) {
                $river = new River();
                $river->setName($item['name']);
                $river->setLength($item['length']);
                $manager->persist($river);
            }

            $manager->flush();
        }
    }

[The Changeset on GitHub](https://github.com/by-examples/symfony-bdd-app-01-rivers/commit/cd4178b2ec1e9f1eddb2cb4aa2c0026c4742a495)

###8.4.3. Load the fixtures into the database

Run the command:

    # Guest OS
    $ php app/console doctrine:fixture -n


###8.4.4. Commit

Commit you changes with:

    # Host OS
    $ git add -A
    $ git commit -m "[RED] Fixtures: YML file and LoadRivers class"

###8.4.5. Create the homepage

Filename: `src/AppBundle/Controller/DefaultController.php`:

    ...
    /**
     * @Route("/", name="homepage")
     */
    public function indexAction()
    {
        $em = $this->getDoctrine()->getManager();

        $entities = $em->getRepository('AppBundle:River')->findAll();

        return $this->render(
            'default/index.html.twig',
            array(
                'entities' => $entities,
            )
        );
    }
    ...

Filename: `app/Resources/views/default/index.html.twig`:

{% raw %}
    {% extends '::base.html.twig' %}
    {% block body %}
        <table>
            <thead>
                <tr>
                    <th>#</th>
                    <th>Name</th>
                    <th>Length</th>
                </tr>
            </thead>
            <tbody>
            {% for entity in entities %}
                <tr>
                    <td>{{ loop.index }}</td>
                    <td>{{ entity.name }}</td>
                    <td>{{ entity.length }}</td>
                </tr>
            {% endfor %}
            </tbody>
        </table>
    {% endblock %}
{% endraw %}

[The Changeset on GitHub](https://github.com/by-examples/symfony-bdd-app-01-rivers/commit/add76d511b572f9f49c7e442d0936fc890353f3b)

###8.5. Behat - GREEN

Run Behat:

    # Guest OS
    $ php app/console cache:clear --env=prod
    $ php app/console cache:warmup --env=prod
    $ mysql -u root < 00-extra/db/create-empty-database.sql
    $ php app/console doctrine:schema:update --force
    $ php app/console doctrine:fixtures:load -n
    $ php app/console fos:user:create admin admin@example.net loremipsum --super-admin
    $ bin/behat

The project is GREEN.

![GREEN](/img/green.png)

###8.6. The commit - GREEN

Commit you changes with:

    # Host OS
    $ git add -A
    $ git commit -m "[GREEN] Fixtures: action/view for homepage"

##9. Final tweaks

##9.1. Link to admin panel

Filename: `app/Resources/views/base.html.twig`:

{% raw %}
    ...
    {% if is_granted('ROLE_USER') %}
        <li>Logged in as {{ app.user.username }}</li>
        <li>
            <a href="{{ path('admin_river') }}">
                Panel
            </a>
        </li>
        <li>
    ...
{% endraw %}

##9.2. Script to reload database

Filename: `reload.bash`:

    ...
    mysql -u root < 00-extra/db/create-empty-database.sql
    php app/console doctrine:schema:update --force

    php app/console doctrine:fixtures:load -n
    php app/console fos:user:create admin admin@example.net loremipsum --super-admin

[The Changeset on GitHub](https://github.com/by-examples/symfony-bdd-app-01-rivers/commit/48c58cb5a8ec0330a8a6490a6afdcaad09f78db5)

###9.3. The commit

Commit you changes with:

    # Host OS
    $ git add -A
    $ git commit -m "[GREEN] Tweaks: menu option and reload.bash"

###9.4. Run the tests

    # Guest OS
    $ ./reload.bash
    $ bin/behat  --format progress

You may run the tests arbitrary number of times:

    $ bin/behat  --format progress
    $ bin/behat  --format progress
    $ bin/behat  --format progress

##10. Visit app with your browser

Run web browser and visit:

    http://localhost:8880/
    http://localhost:8880/app_dev.php/

##11. Remove unnecessary commits

Remove unnecessary commits from your repository:

    # Host OS
    $ git remote rm origin
    $ git branch -D 2.7
    $ git tag | xargs git tag -d
    $ git reflog expire --all --expire=now
    $ git prune
    $ git gc

Now your repository contains only the commits
that you have authored working on this example.

##12. Travis

Thanks to `.travis.yml` and `reload.bash` the example should run
on Travis *out of the box*.
All you have to do is to:

* push the example to your GitHub repo
* configure Travis for this repo

Here is
[the link to Travis output for the original example](https://travis-ci.org/by-examples/symfony-bdd-app-01-rivers).

##13. The Example

You will find [the source code of the example
on GitHub](https://github.com/by-examples/symfony-bdd-app-01-rivers).

For the instruction how to run the example refer to `README.md` file.

