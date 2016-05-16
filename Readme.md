# Docker Orchestration for ENI SEIS Portals

## Installation

1. Install [Docker 1.10.3](https://docs.docker.com/engine/installation/linux/centos/).

2. Install [Docker Compose 1.6.2](https://docs.docker.com/compose/install/).

3. Install [Rancher Compose 0.7.4+](http://www.rancher.com)

## Usage

### Development

In order to be able to edit source-code on your machine using your favorite editor, without having to do it inside a Docker container, you'll have to create a new user on your laptop with `uid=500` and use this user for development:

    $ useradd -u 500 zope-www
    $ usermod -a -G docker zope-www
    $ sudo su - zope-www

Now get the source code:

    $ git clone https://github.com/eea/eea.docker.eni-seis.git
    $ cd eea.docker.eni-seis/devel
    $ docker-compose -f source-code.yml up

Start the application:

    $ docker-compose up

Within your favorite browser head to http://localhost:8080,
add a Plone site and install the following add-ons:
* `EEA Plone buildout profile`
* `eni.seis.theme`
* `eni.seis.content`

Now you are ready to develop Plone Add-ons within `src` folder:

    $ ls -l src/

Once you're done editing, restart the application and test your changes:

    $ docker-compose restart


### Deployment

Pre-requirements:

* [Rancher Compose](http://docs.rancher.com/rancher/rancher-compose/)
* Within Rancher UI register min 1 hosts with label: `eni-seis=yes`

On your laptop:

    $ git clone https://github.com/eea/eea.docker.eni-seis.git
    $ cd eea.docker.eni-seis/deploy

Deploy on staging/demo:

    $ rancher-compose --project-name eni-seis-demo-east --env-file staging-east.env up -d
    $ rancher-compose --project-name eni-seis-demo-south --env-file staging-south.env up -d

Deploy in production:

    $ rancher-compose --project-name eni-seis-east --env-file production-east.env up -d
    $ rancher-compose --project-name eni-seis-south --env-file production-south.env up -d

Upgrade:

    $ rancher-compose --project-name eni-seis --env-file production.env up -d --upgrade

...and confirm that the upgrade went well:

    $ rancher-compose --project-name eni-seis --env-file production.env up -d --confirm-upgrade

...or roll-back:

    $ rancher-compose --project-name eni-seis --env-file production.env up -d --roll-back
