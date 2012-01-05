---
title: Cyclozzo
---
{% extends "listing.html" %}

{% block content %}
{% markdown %}

# Cyclozzo

## Architecture

A Cloud in Cyclozzo's terms is a cluster, running services which manage database, file system, and application instances. Cyclozzo has  a System Cloud exclusively to manage other Application clouds (Application Clouds host your GAE Compatible apps). Each Cloud can be configured to have its own Load Balancer with fail over. Applications hosted can have their own public domain names using any preferred third party DNS service. 

![Cyclozzo Architecture](/images/arch.png)

## System/Management Cloud

* What it does?

System Cloud's main functionality is to deploy Application Clouds, which involves PXE booting a set of machines and associating them with roles (Manager/AppServer).

* Services

    1. Cyclozzo Master is an interface which enables Application Clouds to communicate with System Cloud using RPC.
    2. DHCP Service leases IP's to PXE booted machines based on user configuration.
    3. Statistics Collector catalogs critical resource usage information from each Application servers.
    4. Cyclozzo Console/UI – Cloud Administration Web front-end.

## System Requirement

* Two machines for Master with fail-over and Console.
* At least five machines for a distributed database and file system with high-availability and fail-over.

*Though Cyclozzo could be run in just two machine cluster. We recommend the specs mentioned above.*

Machine Configuration goes here...

## Application Cloud

* What it does?

Manages application instances and reports stats to System cloud.

* Services

    1. Manager scales applications based on load calculated from stats reported by Load Balancer. An Application cloud runs a single instance of Manager service with fail-over.
    2. Satellite service runs all application servers. They start/stop instances as required by Manager service.

## System Requirement

* Two machines for Manager with fail-over
* At least five machines for a distributed database and file system with high-availability and fail-over.
* Additional machines will host applications and take part in data replication.

[FILL UP MACHINE CONFIG HERE]

![Cyclozzo Architecture](/images/setup.png)

# Cyclozzo OSE

## About Cyclozzo OSE

Introduction goes here....

## Building Cyclozzo

Cyclozzo's build system can build `deb` packages from the sources. Once you have all the dependencies ready in the `deps` directory, do 

    cd buildbot
    make all

and publish the debian repository,

    make publish

Now you'll have an `apt` repository with all the packages and dependencies under `repo` directory.

    ls ../repo
    i386    all     x86_64

You have to add the `repo` directory to have FTP access so that `apt` can access the packages.

## Deploying Cyclozzo

Once the repository is ready, you can add the repository url to all the cluster nodes and install Cyclozzo

    sudo add-apt-repository 'deb ftp://<REPOSITORY-ADDRESS-HERE>/all  /'
    sudo add-apt-repository 'deb ftp://<REPOSITORY-ADDRESS-HERE>/x86_64  /'
    sudo apt-get update
    sudo apt-get install cyclozzo-ose

NOTE: You may need to enter a password for SSH keys generated during installation. Leave it empty for passwordless SSH access.

## Configuring Cyclozzo cluster

First of all you need to select a master node. This is the node from where you control all the other nodes and run commands. Once you are on the master node, issue

    sudo nano `cyclozzo --settings`

and change the master and slave node ip addresses. Then do,

    cyclozzo --exchange-keys

to exchange the SSH keys generated during installation to all the slave nodes.

Now, its time to configure/format the cluster

    cyclozzo --configure --format all

## Starting Cyclozzo cluster

    cyclozzo --start cluster

## Starting applications

    cyclozzo --start application --dir guestbook/ --port 8080

## About Stackless Recursion

Please visit [http://gostackless.com/](http://gostackless.com/)

{% endmarkdown %}
{% endblock %}
