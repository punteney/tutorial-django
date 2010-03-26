Starting a new project
=======================
Each new project starts out with the same structure, and base apps installed. Instead of manually creating the project structure and requirements every time I do a new project I've created a base default project on github to use a starting point.

For this tutorial we'll be setting up a very simple zoo site, the project name will be **zoo**.

Default project setup
-----------------------------
First step is create the virtualenv for this the zoo project from the terminal type::

    mkvirtualenv --no-site-packages zoo
    
This will create a new virtualenv called "zoo" a "zoo" folder in ~/projects and activate the zoo virtualenv and changing into the ~/projects/zoo directory. The "--no-site-packages" flag sets it so that none of the globally installed python packages will be available, setting this flag is very helpful when moving the project to production. Now to work on the zoo project just type ``workon zoo`` and it will activate the virtualenv and put you in the ~/projects/zoo directory. When done working on the project run the ``deactivate zoo`` to get out of the virtualenv.

Second step is to download the default project from github::

    workon zoo
    git clone git://github.com/punteney/default_project.git
    rm -rf default_project/.git
    mv default_project zoo_site
    add2virtualenv zoo_site/apps
    
The first line starts the zoo virtualenv, if not already in it. The second line copies down the base project from github for use. The third line removes the .git info as this will be a new project and should not continue to update the 'default_project' on github. The fourth line renames the project from "default_project" to "zoo_site". Finally the last line adds the zoo_site/apps directory to the python path for the virtualenv so that python modules within that directory can be called directly.

Project Structure
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Each project starts with the same default file structure. Later sections will explain the contents of the folders in more detail.

* ~/projects/\ **zoo** - This is the top level folder all the files pertaining to this project will be kept within this folder. This folder is not kept in git for version control. Any items related to this project that won't be kept in version control should be put in the "zoo" top level directory, generally this means anything that is auto generated from the code such as the generated documentation, or build files.
    * **zoo_site**\ / - This is the primary project folder where all the code and configuration files are kept. This folder and all it's files and subfolders (unless specifically excluded/ignored from git) are kept in version control.
        * **project**\ / - This is the django project folder. It holds the settings files, urls, media, and project templates.
        * **apps**\ / - This folder will hold any django apps or python modules used for the project that aren't installed using pip. Often this will be apps specific to this project that aren't meant to be reusable.
            * **utils**\ / - Holds various one off items and helpers for the project that aren't big enough to be their own apps or modules.
        * **config**\ / - Holds various config files for things like Apache, nginx, wsgi, memcache, etc.
        * **deploy**\ / - This folder holds the fabric file and requirements files along with any other items used in the deployment process.
    * **docs**\ / - This folder is empty initially but is there to hold the generated documents for the project.


Initial Requirements
-------------------------------
There are two requirements files in the deploy folder. These are `pip requirements files <http://pip.openplans.org/#requirements-files>`_ containing the required modules.

Requirement Files
^^^^^^^^^^^^^^^^^^^^^^^^^^^
* requirements_all.txt - are required python modules that should be installed on all systems that this project is installed on (dev, staging, production). It contains the following items:
    * `Django <http://www.djangoproject.com/>`_ - The latest released version of Django
    * `ipython <http://ipython.scipy.org/moin/>`_ - An enhanced interactive python shell
    * `python-memcached <http://pypi.python.org/pypi/python-memcached>`_ - A fully python memcache client
    * `south <http://south.aeracode.org/>`_ - Database migrations for django.
    
* requirements_dev.txt - are requirements only needed on the dev server. Most of the items in this file are items to make debugging and testing easier which aren't needed on the production servers.
    * `psycopg2 <http://initd.org/psycopg/>`_ - A database drive for PostgreSQL. On prodution the driver installed through the OS packages is used, so it's not needed on them.
    * `django-debug-toolbar <http://github.com/robhudson/django-debug-toolbar>`_ - A huge help for debugging and optimizing a django site.
    * `werkseug <http://pypi.python.org/pypi/Werkzeug/>`_ - A debugger that that supports interactive debugging on the error pages.
    * `django-extensions <http://pypi.python.org/pypi/django-extensions/>`_ - Various helpful command extensions to django.
    * `fabric <http://fabfile.org>`_ - Used for deploying the site to remove users.

Installing Requirements
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
To install the requirements issue the following commands::

    workon zoo
    pip install -r zoo_site/deploy/requirements_all.txt
    pip install -r zoo_site/deploy/requirements_dev.txt


Project Configuration
--------------------------------
Now that the default project is installed we need to configure it. All the settings are contained within the "zoo_site/settings" directory. The settings files within that directory are:

    * __init__.py - This settings file imports settings in base.py and then looks for an environment variable called "DJANGO_CONFIG_IDENTIFIER" that is used to determine what other settings file should be imported. If no "DJANGO_CONFIG_IDENTIFIER" is specified it will import the dev settings.
    * base.py - This is the primary settings file, the settings in this file apply to all instances of this project (dev, production, staging) and should hold all the common settings.
    * dev.py - Settings specific to the dev environment. 



