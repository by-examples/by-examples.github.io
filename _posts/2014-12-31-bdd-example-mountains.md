---
layout: post
title: "Symfony/BDD example 03: mountains"
excerpt: |
    This is the third example in <strong>Symfony/BDD</strong>
    series. It demonstrates how to use fixtures.
---

##1. Background

I assume that you have already read:

* [Symfony/BDD example 01: Hello, world!](/2014/12/24/bdd-example-hello-world-symfony.html)
* [Symfony/BDD example 02: red, green, blue](/2014/12/30/bdd-example-red-green-blue.html)

The task at hand can be specified as follows:

* there is a file `mountains.yml` that contains a list of mountains
* each mountain has two attributes: `name` and `height`
* the file should be used as a fixture
  (i.e. its contents should be inserted into the database)

Here is the excerpt from the file:

    - { name: "Mount Everest", height: 8880 }
    - { name: "Mount Blanc", height: 4440 }
    - { name: "Kilimanjaro", height: 5550 }

The application requirements:

* I want to create the Symfony application
* the application should consist of one page only
* the page with URL `/` should present
  a table with all the data from YML file
* the table should look like this:

{: .table .table-striped .table-bordered}
Name          | Height
--------------|---------
Mount Everest | 8850
Mount Blanc   | 4810
Kilimanjaro   | 5895

##2. Introductory step

###2.1. Start the project

Create a new directory and clone **my** Symfony Standard repository:

    # Host OS
    $ mkdir symfony-bdd-example-03-fixtures
    $ cd symfony-bdd-example-03-fixtures
    $ git clone git@github.com:by-examples/symfony-standard.git .

###2.2. Create a starting point for the project

Create a new orphan branch that starts at the tag
Symfony Standard v2.6.1:

    # Host OS
    $ git checkout --orphan 2.6.1/bdd-example-03-fixtures v2.6.1

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

##4. Create YML file

Create the file: `data/mountains.yml`:

    - { name: "Mount Everest", height: 8880 }
    - { name: "Mount Blanc", height: 4440 }
    - { name: "Kilimanjaro", height: 5550 }

Commit you changes with:

    # Host OS
    $ git add -A
    $ git commit -m "Fixtures data file: mountains.yml"

##5. Tests - the project is RED

Create the file `features/fixtures.feature` with the
following contents:

    Feature: We want one page with all the mountains...

      Scenario: List mountains
        Given I am on homepage
         Then I should see "Mount Everest"
          And I should see "8880"
         Then I should see "Mount Blanc"
          And I should see "4440"
         Then I should see "Kilimanjaro"
          And I should see "5550"

      Scenario: I want to check the number of records
        When I am on homepage
        Then I should see 3 ".records tbody tr" elements

Run Behat:

    # Guest OS
    $ bin/behat

The project is RED.

![RED](/img/red.png)

Commit the test:

    # Host OS
    $ git add -A
    $ git commit -m "[BDD:RED] tests for fixtures"

##6. Generate Mountain entity

Run the command:

    $ php app/console doctrine:generate:entity

The name of the entity consists of two parts: the bundle's name
and the name for the class.

In this example it is:

    AppBundle:Mountain

Leave default settings for format: `annotation`.

Add two columns:

    name     -   string 255
    height   -   integer

When you are finished press enter (as many times as necessary).

The command generates a class stored in:

    src/AppBundle/Entity/Mountain.php

For this entity ORM will create in the database
a table named `Mountain`.

When you are done commit your changes with:

    $ git commit -m "[BDD:RED] Create Mountain entity"

##6. Write the code to load fixtures

Create the file `src/AppBundle/DataFixtures/ORM/LoadMountains.php`
with the following contents:

    <?php

    namespace AppBundle\DataFixtures\ORM;

    use Doctrine\Common\DataFixtures\FixtureInterface;
    use Doctrine\Common\Persistence\ObjectManager;

    use Symfony\Component\Yaml\Yaml;

    use AppBundle\Entity\Mountain;

    class LoadMountains implements FixtureInterface
    {
        /**
         * {@inheritDoc}
         */
        public function load(ObjectManager $manager)
        {

            $filename = __DIR__ . '/../../../../data/mountains.yml';
            $yml = Yaml::parse(file_get_contents($filename));
            foreach ($yml as $item) {
                $mountain = new Mountain();
                $mountain->setName($item['name']);
                $mountain->setHeight($item['height']);
                $manager->persist($mountain);
            }

            $manager->flush();
        }
    }

Commit your changes with:

    $ git commit -m "[BDD:RED] LoadMountains.php fixtures"

##7. Load the fixtures into your database

Run the command:

    # Guest OS
    $ php app/console doctrine:fixture -n

##8. Create the homepage

This task consist of:

* creating action
* creating view

###8.1. Action

Change the file `src/AppBundle/Controller/DefaultController.php`:

    class DefaultController extends Controller
    {
        /**
         * @Route("/", name="homepage")
         */
        public function indexAction()
        {
            $em = $this->getDoctrine()->getManager();

            $entities = $em->getRepository('AppBundle:Mountain')->findAll();

            return $this->render(
                'default/index.html.twig',
                array(
                    'entities' => $entities,
                )
            );
        }
    }

###8.2. View

Change the file `app/Resources/views/default/index.html.twig`:

{% raw %}
    {% extends 'base.html.twig' %}

    {% block body %}
        <h1>Mountains</h1>
        <table class="records">
            <thead>
                <tr>
                    <th>#</th>
                    <th>Name</th>
                    <th>Height</th>
                </tr>
            </thead>
            <tbody>
            {% for entity in entities %}
                <tr>
                    <td>{{ loop.index }}</td>
                    <td>{{ entity.name }}</td>
                    <td>{{ entity.height }}</td>
                </tr>
            {% endfor %}
            </tbody>
        </table>
    {% endblock %}
{% endraw %}

##9. Run the tests

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
    $ git commit -m "[BDD:GREEN] code for test: fixtures"

##10. Visit app with your browser

Run web browser and visit:

    http://localhost:8880/
    http://localhost:8880/app_dev.php/

You can also view your application in guest using lynx:

    # Guest OS
    $ lynx http://localhost/
    $ lynx http://localhost/app_dev.php/

##11. Test the efficiency

You can test the efficiency of the application with:

    # Guest OS
    $ ab -n 100 http://localhost/

Here are the results I get on my OS X machine (i7/8MB RAM/ssd):

    Server Software:        Apache/2.4.10
    Server Hostname:        localhost
    Server Port:            80

    Document Path:          /
    Document Length:        909 bytes

    Concurrency Level:      1
    Time taken for tests:   2.034 seconds
    Complete requests:      100
    Failed requests:        0
    Total transferred:      117200 bytes
    HTML transferred:       90900 bytes
    Requests per second:    49.17 [#/sec] (mean)
    Time per request:       20.339 [ms] (mean)
    Time per request:       20.339 [ms] (mean, across all concurrent requests)
    Transfer rate:          56.27 [Kbytes/sec] received


My results from Windows machine (i5/4MB RAM/not ssd):

    ...to be done...

##12. Remove unnecessary commits

The history of your project now
contains 15 commits. However, if you run:

    $ git log --oneline --all

you should see more than 1000 commits.

They are mostly the original commits from Symfony Standard.
To remove them, use the following commands:

    $ git remote rm origin
    $ git branch -D 2.7
    $ git tag | xargs git tag -d
    $ git reflog expire --all --expire=now
    $ git prune
    $ git gc

Now your repository contains only 15 commits
that you have authored working on this example.

##13. The Example

You will find [the source code of the example
on GitHub](https://github.com/by-examples/symfony-bdd-example-03-fixtures).

For the instruction how to run the example refer to `README.md` file.
