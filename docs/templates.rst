.. _templates:
Project Templates
=================
Project Templates are GIT repositories that contain a base layout for a project
and are coupled with pillar & state data that Salt uses to provision the
project.

The files inside the template can be parsed as Jinja templates, by marking
them to be parsed in the meta-data, if you need any of your files to be 
updated (which you pretty much always do).

You can also apply the same template logic to path names so that you can apply
transformations to the filesystem to ensure that best practicies for naming
and consistency are applied.

The fact that we have Project Templates and Jinja has a template engine that
we use for files can become confusing. To help with the confusion we will
always refer to our Project Templates with uppercase letters and to Jinja
templates with a lowercase 't'.


Project Template meta-data
--------------------------
The meta-data for Project Templates is stored within a file at the root of
the project named ``stackstrap.yml``. This directory only exists in the 
Template itsef and is not present in the projects that are generated.


stackstrap.yml
~~~~~~~~~~~~~~
When loaing a Template into a Project, Stackstrap will take time to parse files
and paths with the Jinja template engine. This allows you to seed your Template
with project specific name spacing and other goodies like secrets.


Defining your Template for Stackstrap
+++++++++++++++++++++++++++++++++++++
Give StackStrap some information about your Template so that it can help you set 
it up.

* **`template_name`** - a short name to describe the Project Template
* **`template_author`** - your name (formatted as "Full Name <email@address>")
* **`template_description`** - a longer multi-line description of the Project Template

Example::

    template_name: "Flask with Capistrano (nginx + uwsgi)"
    template_author: "Brent Smyth <brent@fatbox.ca>"
    template_description:
        This template contains a Flask app with Flask Script for managing it.
        It is deployed using nginx as the HTTP endpoint and uwsgi as the
        application server.


Cleaning up files
+++++++++++++++++
You may have files in your Template which you do not want in your Projects.
Perhaps you have a README that is only for the Template and another that
you want to use for the Project. In this case just remove the README here
and move the other one into place with a path transform (below)::

    stackstrap:
      cleanup:
        - README

Parsing files as Jinja templates
+++++++++++++++++++++++++++++++++
To tell StackStrap that a file should be passed through the Jinja template
engine when creating a project instance you need to define a list named
``file_templates`` inside the ``stackstrap`` name space containing the file
paths relative to the root of the project::

    stackstrap:
      file_templates:
        - .ruby-gemset
        - deployment/deploy.rb
        - deployment/js_compress.json
        - foundation/config.rb


Transforming filesystem paths
+++++++++++++++++++++++++++++
To tell StackStrap that filesystem paths should be transformed based on the
context data when creating a project instance you need to define a dictionary
named ``path_templates`` inside the ``stackstrap`` name space containing the
original path name as the key and the transformed path name, using the
available context variables, as the value::

    stackstrap:
      path_templates:
        - PROJECT-README: README
        - 'project_app/something/else': 'project_app/something/{{ project.id }}'
        - 'project_app': '{{ project.slug }}_app'

Filesystem paths are transformed in the order they are listed, so list your
more specific matches first as in the example above. Also, the filesystem
transforms are applied after the ``file_templates`` (above) so if you're
specifying a file to both be treated as a template and have its filesystem
path transformed specify the original path name in the ``file_templates``
list and not the transformed one.


Available context variables
+++++++++++++++++++++++++++
When the pillar data and your templates are parsed the following variables are
made available in the context:

* **`name`** - The computer friendly name which you chose for the project. 
  You can use this to seed your project's files and paths.

Salt Files
----------
The salt macros and pillar data are stored in a folder called `salt` at the
root of the project.

TODO: Document this better.


.. _Jinja: http://jinja.pocoo.org/docs/
.. _community repository: https://github.com/stackstrap/stackstrap-salt
.. _Project source code: https://github.com/stackstrap/stackstrap/blob/master/application/stackstrap/projects/models.py

.. vim: set ts=4 sw=4 sts=4 et ai :
