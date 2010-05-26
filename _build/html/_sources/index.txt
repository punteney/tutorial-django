.. Django Project Setup documentation master file, created by
   sphinx-quickstart on Wed Mar 24 10:45:21 2010.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Tutorial for Setting up a Complete Django Project
=================================================
This tutorial focuses on how to setup and manage your django project. It doesn't cover how to write a Django app and project as there are many other tutorials on how to do that. Items this tutorial does include:

#. :doc:`Setting up a local dev environment (on OS X) <local-setup>`
#. :doc:`Starting a new project <project-start>`
#. :doc:`Building out the project <project-build>`
#. :doc:`Testing <testing>`
#. :doc:`Documentation <documentation>`
#. :doc:`Pushing the project to production <pushing-live>`
#. :doc:`Ongoing work process <ongoing>`

The tutorial will be easier to follow with a basic understanding of python, Django, and command line usage. If you aren't familiar with these you might want to check out some of the :doc:`resources <learning-resources>` to familiarize yourself with them first first.

.. note:: 

    This tutorial is **how I** set things up. It describes methods I've picked up over the last 3 or 4 years that work well for me. There are many other equally valid methods to use. Take this tutorial as a guide, if something doesn't work for your process change it, if there is a better way to accomplish something let me know.


Contents:

.. toctree::
    :maxdepth: 2
    
    local-setup.rst
    project-start.rst
    project-build.rst
    testing.rst
    documentation.rst
    pushing-live.rst
    ongoing.rst
    cheat-sheet.rst
    learning-resources.rst
    

TODO
    
* Output of workon command in the virtualenv install
* Create the docs folder
* Refactor the config files to use fabric templates
* Configure postgres to allow access to the DB from the specific IP's without passwords


Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

