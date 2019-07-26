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

Start the debug container:

    $ docker-compose up -d

Then connect with bash to the debug container:

    $ docker exec -it devel_debug_1 bash
    
Inside the container start the Plone instance in foreground:

    $ bin/standalone fg

Within your favorite browser head to http://localhost:8080,
add a Plone site and install the following add-ons:
* `EEA Plone buildout profile`
* `eni.seis.theme`
* `eni.seis.content`

Now you are ready to develop Plone Add-ons within `src` folder:

    $ ls -l src/

You can do normal Plone development, including setting breakpoints 
with pdb. The eggs used in the buildout are in the parent folder of
the default location. If you need to debug something, to set a 
breakpoint, you can use vi to edit files.

### EEA Rancher Catalog
https://github.com/eea/eea.rancher.catalog/tree/master/templates/eni-seis-eionet

### Demo website deployment (deprecated)

Pre-requirements:

* [Rancher CLI](http://rancher.com/docs/rancher/v1.2/en/cli/)
* Within Rancher UI register min 1 hosts with label: `eni-seis=yes`

On your laptop:

    $ git clone https://github.com/eea/eea.docker.eni-seis.git
    $ cd eea.docker.eni-seis/deploy

Set Postfix credentials:

    $ cp .secret.example .secret
    $ vim .secret
    $ source .secret

Deploy on staging/demo or production:

    $ rancher config (add your stack and your API key)
    $ rancher up -d -s eni-seis-eionet

Upgrade:

    $ source .secret
    $ rancher up -d --upgrade --interval 300000 --batch-size 1 -s eni-seis-eionet

...and confirm that the upgrade went well:

    $ rancher up -d --confirm-upgrade -s eni-seis-eionet

...or roll-back:

    $ rancher up -d --roll-back -s eni-seis-eionet
