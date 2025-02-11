.. image:: https://github.com/jugmac00/flask-reuploaded/workflows/CI/badge.svg?branch=master
   :target: https://github.com/jugmac00/flask-reuploaded/actions?workflow=CI
   :alt: CI Status

.. image:: https://coveralls.io/repos/github/jugmac00/flask-reuploaded/badge.svg?branch=master
    :target: https://coveralls.io/github/jugmac00/flask-reuploaded?branch=master

.. image:: https://img.shields.io/pypi/v/flask-reuploaded   
    :alt: PyPI
    :target: https://github.com/jugmac00/flask-reuploaded

.. image:: https://img.shields.io/pypi/pyversions/flask-reuploaded   
    :alt: PyPI - Python Version
    :target: https://pypi.org/project/Flask-Reuploaded/

.. image:: https://img.shields.io/pypi/l/flask-reuploaded
    :target: https://github.com/jugmac00/flask-reuploaded/blob/master/LICENSE


Flask-Reuploads
================

Flask-Reuploads is a copy of Flask-Reuploaded that provides file uploads for Flask.


Notes on this package
---------------------

This is an independently maintained version of `Flask-Uploads`
based on the 0.2.1 version of the original,
but also including four years of unreleased changes,
at least not released to PyPI.

Noteworthy is the fix for the `Werkzeug` API change.


Goals
-----

- provide a stable drop-in replacement for `Flask-Uploads`
- regain momentum for this widely used package
- provide working PyPI packages


Migration guide from `Flask-Uploads`
------------------------------------

Incompatibilities between Flask-Reuploaded and Flask-Uploads
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

As already mentioned,
staying compatible with `Flask-Uploads` is one of this project's goals.

Nevertheless, there are the following known incompatibilities:

- the `patch_request_class` helper function has been removed;
  the function was only necessary for Flask 0.6 and earlier.
  Since then you can use Flask's own
  `MAX_CONTENT_LENGTH <https://flask.palletsprojects.com/en/1.1.x/config/#MAX_CONTENT_LENGTH>`_
  environment variable,
  so you don’t read more than this many bytes from the incoming request data.
- `autoserve` of uploaded images now has been deactivated;
  this was a poorly documented "feature",
  which even could have lead to unwanted data disclosure;
  if you want to activate the feature again,
  you need to set `UPLOADS_AUTOSERVE=True`

Uninstall and install
~~~~~~~~~~~~~~~~~~~~~

If you have used `Flask-Uploads` and want to migrate to `Flask-Reuploaded`,
you only have to install `Flask-Reuploaded` instead of `Flask-Uploads`.

That's all!

So, if you use `pip` to install your packages, instead of ...

.. code-block:: bash

    $ pip install `Flask-Uploads`  # don't do this! package is broken

... just do ...

.. code-block:: bash

    $ pip install `Flask-Reuploads`

`Flask-Reuploads` is not a drop-in replacement.

Instead of importing from "flask_reuploaded" you
need to import from "flask_reuploads".

That means, I have changed the package name from 
flask_reuploaded to flask_reuploads to avoid any 
conflicts when using pipreqs.



Installation
------------

.. code-block:: bash

    $ pip install Flask-Reuploads


Getting started
---------------

create an UploadSet

.. code-block:: python

    from flask_reuploads import IMAGES

    photos = UploadSet("photos", IMAGES)

configure your Flask app and this extension

.. code-block:: python

    app.config["UPLOADED_PHOTOS_DEST"] = "static/img"
    app.config["SECRET_KEY"] = os.urandom(24)
    configure_uploads(app, photos)

use `photos` in your view function

.. code-block:: python

    photos.save(request.files['photo'])

See below for a complete example.


Documentation
-------------

You can find the documentation at:

https://flask-reuploaded.readthedocs.io/en/latest/

You can generate the documentation locally:

.. code-block:: bash

    tox -e docs

You can update the dependencies for documentation generation:

.. code-block:: bash

    tox -e upgradedocs


Minimal example application
----------------------------


Application code, e.g. main.py
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: python

    import os

    from flask import Flask, flash, render_template, request
    # please note the import from `flask_uploads` - not `flask_reuploaded`!!
    # this is done on purpose to stay compatible with `Flask-Uploads`
    from flask_reuploads import IMAGES, UploadSet, configure_uploads

    app = Flask(__name__)
    photos = UploadSet("photos", IMAGES)
    app.config["UPLOADED_PHOTOS_DEST"] = "static/img"
    app.config["SECRET_KEY"] = os.urandom(24)
    configure_uploads(app, photos)


    @app.route("/", methods=['GET', 'POST'])
    def upload():
        if request.method == 'POST' and 'photo' in request.files:
            photos.save(request.files['photo'])
            flash("Photo saved successfully.")
            return render_template('upload.html')
        return render_template('upload.html')


HTML code for `upload.html`
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: html

    <!doctype html>
    <html lang=en>
    <head>
        <meta charset=utf-8>
        <title>Flask-Reuploaded Example</title>
    </head>
    <body>
        {% with messages = get_flashed_messages() %}
        {% if messages %}
        <ul class=flashes>
        {% for message in messages %}
            <li>{{ message }}</li>
        {% endfor %}
        </ul>
        {% endif %}
        {% endwith %}

    <form method=POST enctype=multipart/form-data action="{{ url_for('upload') }}">
        <input type=file name=photo>
        <button type="submit">Submit</button>
    </form>
    </body>
    </html>


Project structure
~~~~~~~~~~~~~~~~~

The project structure would look as following...

.. code-block:: bash

    ❯ tree -I "__*|h*"
    .
    ├── main.py
    ├── static
    │   └── img
    └── templates
        └── upload.html


Running the example application
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In order to run the application,
you have to enter the following commands...

.. code-block:: bash

    ❯ export FLASK_APP=main.py

    ❯ flask run

Then point your browser to `http://127.0.0.1:5000/`.


Contributing
------------

Contributions are more than welcome.

Please have a look at the `open issues <https://github.com/jugmac00/flask-reuploaded/issues>`_.

There is also a `short contributing guide <https://github.com/jugmac00/flask-reuploaded/blob/master/CONTRIBUTING.rst>`_.
