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

    docker run hello-world

#### Pull INSPIRE Labs image

We will use the INSPIRE Labs image we prepared for you:

    docker pull inspirehep/inspire:demo


#### Pull INSPIRE overlay sources

While the docker image is downloading, we can grab the INSPIRE overlay sources which we will modify and play with.

First open a new terminal window and create a new folder for the our sources.

    mkdir -p ~/src
    cd ~/src

Now we `git clone` the sources:

    git clone https://github.com/inspirehep/inspire-next.git

#### Run the container instance

Assuming our docker image is downloaded we are going to initiate the container instance and run our INSPIRE labs site.

First we enter the directory which the INSPIRE sources were put by our `git clone` command:

     $ cd inspire-next

Now, depending on your system, we run the container:

On MAC OSx:

     docker run -v $(pwd):/src/inspire-next -i -t -p 4000:4000 -e EXTERNAL_IP="$(docker-machine ip default)" inspirehep/inspire:demo


On GNU/Linux:

     docker run -v $(pwd):/src/inspire-next -i -t -p 4000:4000 inspirehep/inspire:demo


*Make sure your current working directory is the INSPIRE overlay source folder.

### Templates and blueprints
--------------------------------

#### Flask Blueprints, Adding a static page

Firstly, we will have a look at an example of how Invenio uses [Flask Blueprints](http://flask.pocoo.org/docs/0.10/blueprints/) and create a static page

**Step 1**: Create a "view" on the overlay (`base/views.py`) file, so Flask/Invenio knows to direct to the new page

**Step 2**: Create a template to be rendered by the view method we made previously, by extending the "page.html" and adding it on the `base/templates` directory of the overlay

**Step 3**: Restart docker/server

Code Samples: [About page](https://github.com/inspirehep/inspire-next/blob/master/inspire/base/templates/inspire/about.html), [views.py](https://github.com/inspirehep/inspire-next/blob/master/inspire/base/views.py),  [page.html(overlay)](https://github.com/inspirehep/inspire-next/blob/master/inspire/base/templates/page.html), [page.html(invenio)](https://github.com/inveniosoftware/invenio/blob/maint-2.1/invenio/base/templates/page_base.html)

--------------
#### Templates

Here we are going to change the ["Login"](http://localhost:4000/youraccount/login) page to display an extra Github button for authentication

**Step 1**: On the overlay we open( or create) the "login.html" file(`base/templates/accounts/login.html`) and 
add a link, so users can click to navigate to the Github page for authentication

**Step 2**: Now, on the "config.py" file of the overlay, we need to add the oauth configurations and credentials (secret/keys given from github)

**Step 3**: Restart docker/server

Code Samples: [Github oauth code](https://github.com/inveniosoftware/invenio/blob/maint-2.1/invenio/modules/oauthclient/contrib/github.py), [config.py(overlay)](https://github.com/inspirehep/inspire-next/blob/master/inspire/config.py), [login page(overlay)](https://github.com/inspirehep/inspire-next/blob/master/inspire/base/templates/accounts/login.html)

-------
#### Formatting record briefs

Finally, we are going to do some changes on the record briefs and the way they are displayed on [search results](http://localhost:4000/search?p=&cc=HEP)

Let say for example we want to modify the way 'Author' records briefs are displayed

**Step 1**: On the overlay we open( or create) the ["hb.yml"](https://github.com/inspirehep/inspire-next/blob/master/inspire/modules/formatter/output_formats/hb.yml) file(`modules/formatter/output_formats/hb.yml`) and add the configurations we need to customize the output format and connect it with a template depending on record fields that we are interested (basically 'collections')

**Step 2**: Next, it is important to have the templates specified on the "yml" file for Invenio to render. This templates should be located on overlay (`base/templates/format/record/`) and they are simple Jinja templates.

Code Samples: [hb.yml (invenio)](https://github.com/inveniosoftware/invenio/blob/maint-2.1/invenio/modules/formatter/output_formats/hb.yml), [hb.yml (overlay)](https://github.com/inspirehep/inspire-next/blob/master/inspire/modules/formatter/output_formats/hb.yml), [Author brief template](https://github.com/inspirehep/inspire-next/blob/master/inspire/base/templates/format/record/Author_HTML_brief.tpl)






### Adding a new submission (Deposit)
--------------------------------

First we are going to have a look at a real world example present in https://qa.inspirehep.net/submit/literature/create

This submission allows users to suggest articles to be added into INSPIRE.

And now we will have a look at a minimal deposition and add it to our site. The code for this demo can be found in:

https://github.com/inspirehep/inspire-next/tree/iugw2015-deposit-demo


### Adding a harvesting workflow
--------------------------------


# Thanks and happy hacking!
