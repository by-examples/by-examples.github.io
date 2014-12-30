---
layout: post
title: Speeding up Symfony with Varnish
excerpt: |
    Varnish is a reverse HTTP proxy,
    sometimes referred to as a HTTP accelerator or a web accelerator.
    It can speedup the process of serving content over HTTP protocol
    60 times!
---

##1. Background

The assumptions are following:

* your <a href="https://www.digitalocean.com/?refcode=dc864ecee4dc">Digital Ocean</a>
  droplet LAMP has already been set up
* you know how to open ssh session to your droplet
* Symfony Standard default web page is already available at http://localhost/ within your droplet

If you need additional assistance to meet the above assumptions
refer to:

* <a href="http://by-examples.net/2014/12/15/using-ssh-key-for-digital-ocean.html">A tutorial on installing ssh keys for Digital Ocean droplet</a>
* <a href="http://by-examples.net/2014/12/16/running-symfony-standard-on-digital-ocean.html">A tutorial on setting up Symfony application on Digital Ocean droplet</a>

##2. Install lynx and visit the default web page

Install lynx:

    $ sudo apt-get update -y
    $ sudo apt-get install lynx-cur -y

and visit your web site:

    $ lynx localhost

If you are using Symfony Standard,
you should see the page containing two words:

                        Welcome!
    Homepage.

You can quit lynx by pressing Q key on your keyboard.

##3. Install the software to benchmark your site

I will use `ab` application to benchmark the site.
To install it run:

    $ sudo apt-get install apache2-utils -y

##4. Run the tests to check current speed of your site

    $ ab -c 10 -n 1000 http://localhost/

The result you will get will be similar to:

    Concurrency Level:      10
    Time taken for tests:   9.432 seconds
    Complete requests:      1000
    Failed requests:        0
    Total transferred:      525000 bytes
    HTML transferred:       248000 bytes
    Requests per second:    106.03 [#/sec] (mean)
    Time per request:       94.316 [ms] (mean)
    Time per request:       9.432 [ms] (mean, across all concurrent requests)
    Transfer rate:          54.36 [Kbytes/sec] received

Thus, the current speed may be estimated as **100 requests per second**.

##5. Install Varnish

    $ sudo apt-get install varnish -y

##6. Configure Varnish

Modify the contents of `/etc/default/varnish` file.

Configure Varnish to respond to queries received on port 8080.

You can achieve this with the following settings:

    # File: /etc/default/varnish

    DAEMON_OPTS="-a :8080 \
                 -T localhost:6082 \
                 -f /etc/varnish/default.vcl \
                 -s malloc,1024m"

Next modify the contents of `/etc/varnish/devault.vcl` file.

With the following settings Varnish will use Apache running on port 80:

    # File: /etc/varnish/default.vcl

    backend default {
        .host = "127.0.0.1";
        .port = "80";
    }

##7. Restart varnish

    $ sudo service varnish restart

##8. What have we just done?

Right now, your server listens for HTTP requests using two ports:

* 80 - standard port used by Apache
* 8080 - port used by Varnish

When Varnish receives a request for the first time, it contacts Apache to
to get the answer. After that, Varnish responds with cached
answer.

##9. Run the tests again

This time use port 8080 for tests:

    $ ab -c 10 -n 1000 http://localhost:8080/

You should get results similar to the ones shown below:

    Concurrency Level:      10
    Time taken for tests:   0.165 seconds
    Complete requests:      1000
    Failed requests:        0
    Total transferred:      538000 bytes
    HTML transferred:       248000 bytes
    Requests per second:    6070.87 [#/sec] (mean)
    Time per request:       1.647 [ms] (mean)
    Time per request:       0.165 [ms] (mean, across all concurrent requests)
    Transfer rate:          3189.58 [Kbytes/sec] received

Now, the speed may be estimated as **6000 requests per second**.

The difference is really amazing:

{: .table .table-striped}
|Port  | Content served by | queries/second |
| ------------------------------------------|
| 80   | Apache            | 100            |
| 8080 | Varnish           | 6000           |


<div class="alert alert-info" role="alert">
<strong>
<i class="fa fa-thumbs-o-up"></i>
With Varnish the default Symfony Standard page
is served x60 times faster.
</strong>
</div>


##7. Reading list

1. <a href="http://en.wikipedia.org/wiki/ApacheBench">Apache Benchmark (ab)</a>
2. Piotr Pasich: <a href="http://piotrpasich.com/wordpress-varnish/">How to speed up your WordPress 100x in 10 minutes</a>
3. <a href="https://www.varnish-cache.org/">Varnish website</a>
4. <a href="http://www.technoreply.com/solving-dreaded-varnish-503-error/">Solving the dreaded Varnish 503 error</a>
