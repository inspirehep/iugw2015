# Invenio User Group Workshop 2015

Inspired from https://github.com/jirikuncar/iugw2015.git

## Invenio 2 Customization Hands-on

In this session we will try to customize an Invenio 2.x installation by adding new formats, pages, submissions and more.


### 0. What do I need on my machine?
--------------------------------

Like the other sessions we will work with **Docker**.

![Docker](https://www.docker.com/sites/all/themes/docker/assets/images/logo.png)

Please follow this [link](https://www.docker.com) and install Docker tool chain on your machine. E.g. [MAC OSx](
https://www.docker.com/toolbox).


### 1. Verify your docker installation

On Mac OSx, open `Docker Quickstart Terminal`, on GNU/Linux open a normal terminal window.

Now test your docker setup:

    $ docker run hello-world

### 2. Pull INSPIRE Labs image

We will use the INSPIRE Labs image we prepared for you:

    $ docker pull inspirehep/inspire:demo

### 3. Run the container instance

Now we are going to initiate the container instance and run our INSPIRE labs site.

On MAC OSx:

     docker run -v ~/src/inspire-next:/src/inspire-next -i -t -p 4000:4000 -e EXTERNAL_IP="$(docker-machine ip default)"  inspire-prod


On GNU/Linux:

     docker run -v ~/src/inspire-next:/src/inspire-next -i -t -p 4000:4000 inspire-prod
