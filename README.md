# Invenio User Group Workshop 2015

Inspired from https://github.com/jirikuncar/iugw2015.git

## Invenio 2 Customization Hands-on

In this session we will try to customize an Invenio 2.x installation by adding new formats, pages, submissions and more.


### Setup & Requirements
--------------------------------

Like the other sessions we will work with **Docker**.

![Docker](https://www.docker.com/sites/all/themes/docker/assets/images/logo.png)

Please follow this [link](https://www.docker.com) and install Docker tool chain on your machine. E.g. [MAC OSx](
https://www.docker.com/toolbox).


#### Verify your docker installation

On Mac OSx, open `Docker Quickstart Terminal`, on GNU/Linux open a normal terminal window.

Now test your docker setup:

    $ docker run hello-world

#### Pull INSPIRE Labs image

We will use the INSPIRE Labs image we prepared for you:

    $ docker pull inspirehep/inspire:demo


#### Pull INSPIRE overlay sources

While the docker image is downloading, we can grab the INSPIRE overlay sources which we will modify and play with.

First open a new terminal window and create a new folder for the our sources.

    $ mkdir ~/invenioworkshop2015
    $ cd invenioworkshop2015

Now we `git clone` the sources:

   $ git clone https://github.com/inspirehep/inspire-next.git

#### Run the container instance

Assuming our docker image is downloaded we are going to initiate the container instance and run our INSPIRE labs site.

First we enter the directory which the INSPIRE sources were put by our `git clone` command:

     $ cd inspire-next

Now, depending on your system, we run the container:

On MAC OSx:

     $ docker run -v $(pwd):/src/inspire-next -i -t -p 4000:4000 -e EXTERNAL_IP="$(docker-machine ip default)" inspirehep/inspire:demo


On GNU/Linux:

     $ docker run -v $(pwd):/src/inspire-next -i -t -p 4000:4000 inspirehep/inspire:demo

### Templates and blueprints
--------------------------------




### Adding a new submission (Deposit)
--------------------------------

The code for this demo can be found in:

https://github.com/inspirehep/inspire-next/tree/iugw2015-deposit-demo


### Adding a harvesting workflow
--------------------------------


# Thanks and happy hacking!
