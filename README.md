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

     cd inspire-next

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

Now we want to add an OAI-PMH harvest workflow.

In Invenio 2.0 this is done a bit more programmatically than in the past using pluggable Python functions and classes.

With the help of [invenio-oaiharvester](https://invenio-oaiharvester.readthedocs.org/en/latest/) we will harvest records from an OAI-PMH repository and feed it into an ingestion workflow.

This ingestion workflow will be a set of tasks, or Python functions, that will convert the metadata format to JSON and ask for upload into the system.

The code for this demo can be found in [here](https://github.com/inspirehep/inspire-next/tree/iugw2015-oaiworkflows-demo)


#### Adding the initial workflow

We need to add the appropriate files for the workflows [registry](http://flask-registry.readthedocs.org/en/latest/userguide.html) to pick up our workflow:

    mkdir -p  inspire/modules/oaiharvester/workflows
    touch inspire/modules/oaiharvester/workflows/__init__.py
    touch inspire/modules/oaiharvester/workflows/demo_oai.py

Then inside `demo_oai.py` we add a base workflow definition:

```python
class demo_oai(object):

    object_type = "OAI-PMH"

    workflow = []
```

This is our base workflow definitions where we define our workflow. However, right now it does not do much!

Let's add some tasks.

#### Adding the initial tasks

A task is a Python function which is given two arguments. The object, or record, being processed in the workflow (`obj`), and the current engine execution (`eng`) that gives you access to halt, stop or jump around in the workflow.

In our case, the workflow is given the OAI-PMH XML as a text string so first we should convert the XML format into our data model as a Python dictionary. This is tedious work, so fortunately we will just use the existing implementations available in INSPIRE.


```python
from invenio_oaiharvester.tasks.records import convert_record_to_json
from inspire.modules.converter.tasks import convert_record

class demo_oai(object):

    object_type = "OAI-PMH"

    workflow = [
        convert_record("oaiarXiv2inspire_nofilter.xsl"),
        convert_record_to_json,
    ]
```

#### Adding an approval step

Now we have the data in our JSON data model as a Python dictionary. Great, now we could halt the workflow and ask a cataloger to approve this record. If approved, we upload the record. Simple (well..).

Let's create the halt task and add it to the workflow.

```python
def halt_for_approval(obj, eng):
    eng.halt(action="demo_approval", msg="This is a demo approval")

class demo_oai(object):

    object_type = "OAI-PMH"

    workflow = [
        convert_record("oaiarXiv2inspire_nofilter.xsl"),
        convert_record_to_json,
        halt_for_approval,
    ]
````

You may notice we added a `action` argument to the halt task. This is important for the Holding Pen integration - to provide the interactivity to approve/reject records. Similar to workflows, we can add out custom action alongside templates and JavaScript files.

We need to add the appropriate files for the actions [registry](http://flask-registry.readthedocs.org/en/latest/userguide.html) to pick up our workflow:

    mkdir -p  inspire/modules/oaiharvester/actions
    touch inspire/modules/oaiharvester/actions/__init__.py
    touch inspire/modules/oaiharvester/actions/demo_approval.py

Then add the action code in `demo_approval.py`. We will re-use the existing code from other INSPIRE actions to simplify this step by using inheritance for the templating.

```python
from inspire.modules.workflows.actions.core_approval import core_approval

class demo_approval(core_approval):

    """Our demo approval.""

    def resolve(self, bwo):
        """Resolve the action taken in the approval action."""
        bwo.remove_action()
        extra_data = bwo.get_extra_data()

        value = request.form.get("value", None)
        extra_data["approved"] = value in ('accept', 'accept_core')

        bwo.set_extra_data(extra_data)
        bwo.save()

        bwo.continue_workflow(delayed=True)

        if extra_data["approved"]:
            return {
                "message": "Suggestion has been accepted!",
                "category": "success",
            }
        else:
            return {
                "message": "Suggestion has been rejected",
                "category": "warning",
            }

```

The `resolve()` function is handling the response back from the user.

```python
class demo_oai(object):

    object_type = "OAI-PMH"

    workflow = [
        convert_record("oaiarXiv2inspire_nofilter.xsl"),
        convert_record_to_json,
        halt_for_approval,
    ]
```

Then we can add a control flow using a builtin IF function and add a task that checks if it was approved. If so, we create the record on the system - so we add a create_record step.

```python

from invenio_workflows.tasks.logic_tasks import (
    workflow_if,
    workflow_else,
)

def was_approved(obj, eng):
    if obj.extra_data.get('approved'):
        return True
    return False


def create_record(obj, eng):
    Record.create(obj.data)

class demo_oai(object):

    object_type = "OAI-PMH"

    workflow = [
        convert_record("oaiarXiv2inspire_nofilter.xsl"),
        convert_record_to_json,
        halt_for_approval,
        workflow_if(was_approved),
        [
            create_record,
        ],
    ]
```

#### Running workflows with inveniomanage oaiharvester

Thanks to the new CLI integration in Invenio 2.0, invenio-oaiharvester has a CLI tool available through `inveniomanage`.

```console
inveniomanage oaiharvester --help
```

There is `get`, and `queue` options available. For this demo we'll use `get`.

```console
inveniomanage oaiharvester get --help
```

Let's see some example output from the harvester:

```console
inveniomanage oaiharvester get -u http://export.arxiv.org/oai2 -i oai:arXiv.org:1507.07286 -m arXiv
```

Now let's hook in our workflow:

```console
inveniomanage oaiharvester get -u http://export.arxiv.org/oai2 -i oai:arXiv.org:1507.07286 -m arXiv -o workflow -w demo_oai
```

# Thanks and happy hacking!
