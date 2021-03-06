Starting a new project
=======================
Each new project starts out with the same structure, and base apps installed. Instead of manually creating the project structure and requirements every time I use a default project on github as the starting point.

For this tutorial we'll be setting up a zoo site, the project name will be **zoo**.

Default project setup
-----------------------------
First create the virtualenv for the zoo project from the terminal type::

    mkvirtualenv --no-site-packages zoo
    
This will create a new virtualenv called "zoo" a "zoo" folder in ~/projects and activate the zoo virtualenv and change into the ~/projects/zoo directory. The "--no-site-packages" flag sets it so that none of the globally installed python packages will be available, setting this flag now is very helpful when moving the project to production. In the future to work on the zoo project just type ``workon zoo`` and it will activate the virtualenv and put you in the ~/projects/zoo directory. When done working on the project run the ``deactivate`` to get out of the ``zoo`` virtualenv.

Next download the default django project from github::

    workon zoo
    git clone git://github.com/punteney/default_project.git
    mv default_project zoo
    rm -rf zoo/.git
    add2virtualenv zoo/website zoo/apps
    
The first line starts the zoo virtualenv. The second line copies down the base project from github for use. The third line renames the project from "default_project" to "zoo". The fourth line removes the .git info as this will be a new project and should not continue to update the 'default_project' on github. Finally the last line adds the zoo/website and zoo/apps directory to the python path for the virtualenv so that python modules within those directory can be called directly.

Project Structure
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Each project starts with the same default file structure. Later sections will explain the contents of the folders in more detail.

* ~/projects/\ **zoo** - This is the top level folder, all the files pertaining to this project will be kept within this folder. This folder is not kept in git for version control. Any items related to this project that won't be kept in version control should be put in this top level directory, generally this means anything that is auto generated from the code such as the generated documentation, or build output files.
    * **zoo**\ / - This is the primary project folder where all the code and configuration files are kept. This folder and all it's files and subfolders (unless specifically excluded/ignored from git) are kept in version control.
        * **website**\ / - This is the django project folder for the site. It holds the settings files, urls, media, and project templates.
        * **apps**\ / - This folder will hold any django apps or python modules used for the project that aren't installed using pip. Often this will be apps specific to this project that aren't meant to be reusable.
            * **utils**\ / - Holds various one off items and helpers for the project that aren't big enough to be their own apps or modules.
        * **config**\ / - Holds various config files for things like Apache, nginx, wsgi, memcache, etc.
        * **requirements**\ / - This folder holds the requirements files for packages that should be installed by pip.
        * **bin**\ / - This folder holds any scripts or executables for the project.
            * **dserv**\ / - Shortcut command for the ``website/manage.py runserver`` command
            * **dshell**\ / - Shortcut command for the ``website/manage.py shell`` command
            * **reset_db**\ / - Shortcut command for deleting the dev.db then running syncdb and migrate, and loading the test data fixtures.
    * **docs**\ / - This folder is empty initially but is there to hold the generated documents for the project.


Requirements
-------------------------------
There are three `pip requirements files <http://pip.openplans.org/#requirements-files>`_ in the requirements folder that contain the needed packages to be installed.

Requirement Files
^^^^^^^^^^^^^^^^^^^^^^^^^^^
* production.txt - Contains the required python modules that should be installed on the production server. This requirement file is then included in the other requirement files, so by default items in this file will be installed in all environments (production, staging, dev). It contains the following items:
    * `Django <http://www.djangoproject.com/>`_ - The latest released version of Django
    * `ipython <http://ipython.scipy.org/moin/>`_ - An enhanced interactive python shell
    * `python-memcached <http://pypi.python.org/pypi/python-memcached>`_ - A fully python memcache client
    * `south <http://south.aeracode.org/>`_ - Database migrations for django.
    
* dev.txt - Contains the requirements needed on the dev server. Most of the items in this file are items to make debugging and testing easier which aren't needed on the servers.
    * The first line ``-r production.txt`` includes all the packages in the production.txt requirements file.
    * `django-debug-toolbar <http://github.com/robhudson/django-debug-toolbar>`_ - A huge help for debugging and optimizing a django site.
    * `werkseug <http://pypi.python.org/pypi/Werkzeug/>`_ - A debugger that that supports interactive debugging on the error pages.
    * `django-extensions <http://pypi.python.org/pypi/django-extensions/>`_ - Various helpful command extensions to django.
    * `fabric <http://fabfile.org>`_ - Used for deploying the site to remote servers.

* staging.txt - Contains the requirements needed on the staging server. As staging should in general match the production server as closely as possible this will generally just install the production packages.
    * The first line ``-r production.txt`` includes all the packages in the production.txt requirements file.

Installing Requirements
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
To install the requirements issue the following commands::

    workon zoo
    pip install -r zoo/requirements/dev.txt

Project Configuration
--------------------------------
Now that the default project is installed we need to configure it. All the settings are contained within the "zoo/website/settings" directory. The settings files within that directory are:

    * __init__.py - This settings file looks for an environment variable called "DJANGO_ENVIRONMENT" that is used to determine what other settings file should be imported. If no "DJANGO_ENVIRONMENT" is specified it will import the dev settings. In general unless you need to add a new "environment" you shouldn't need to change this file.
    * base.py - This is the primary settings file. The settings in this file apply to all environments of the project (dev, production, staging) and should hold all the common settings for the project. In general this file should be thought of as the "settings" file.
    * Environment specific settings - Each of these settings files will be applied to the different types of servers. The first thing each of these files does is import the base.py settings so any setting defined in these settings files will override the settings within base.py. Common settings to override are the database, email, cache, and debug settings. In general the fewer settings you include in these files the better.
        * dev.py - Settings specific to the dev environment. 
        * production.py - Settings specific to the production environment
        * staging.py - Settings specific to the staging environment
    * local.py - This is for settings that are specific to the server or settings that shouldn't be included in the repository. The most common settings for local.py are the database password and secret key, if the source code repository isn't secure. local.py is the last settings file included so any settings in it will override any previously defined settings. If  a local.py settings is needed it should be created in the "website/settings" folder. As this file isn't kept in the source control system you want to minimize the number of settings kept within it.


Updates in base.py
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Go through the base.py file updating the appropriate settings. The ones that commonly need to be changed are:

* ADMINS - The name and email address of the admins for this project, often initially this will just be you and your email.
* MANAGERS - If different from the ADMINS.
* EMAIL\_\* - The various email sending settings
* TIME_ZONE
* LANGUAGE_CODE
* SECRET_KEY - The unique secret key for your project. You can create a secret key using the python interactive shell::

>>> from random import choice
>>> ''.join([choice('abcdefghijklmnopqrstuvwxyz0123456789!@#$%^&*(-_=+)') for i in range(50)])

* CACHE_MIDDLEWARE_KEY_PREFIX - A name specific to this project. For this tutorial project set it to 'zoo'.

Updates in dev.py
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
No changes should be needed for the dev settings file to get started on the project.


Database Creation
--------------------------------
In the dev.py settings file it specifies a "dev.db" SQLite database. This is placed in a "data" folder outside of the project so as to not be included in the repository. All that needs to be done is to create the data folder::

    mkdir ~/projects/zoo/data

With the data directory created Django will automatically create the SQLite dev.db when "syncdb" is run.


Initial DB Syncing
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
From the command line run syncdb and create the superuser when it requests it::

    workon zoo
    cd zoo/website/
    ./manage.py syncdb
    
Adding the project to Version Control
-----------------------------------------
Go to `github <http://github.com>`_ and login if you aren't already logged in. From your github dashboard page click the "new repository" button. For the "Project Name" enter 'tutorial_zoo_site', the other fields you can leave blank and leave the access to this repository to be 'Anyone' and create the repository.

.. note:: 

    By setting the repository to be viewable by anyone any information that is within the repository will be public, so **do not** use the actual secret key or database passwords that are used for anything other than this sample project locally. On actual projects the source code repository will either be private and secure or you can use the local.py settings file to keep the private information out of the repository.

The next page on github will give you next step directions, some of which have been done earlier. This is the subset of the steps that still need to be done::

    workon zoo
    cd zoo
    git init
    ga * .gitignore
    gcm -m 'Initial commit with default project setup'
    git remote add origin git@github.com:YOUR-GITHUB-USERNAME/tutorial_zoo_site.git
    gps origin master

Make sure to replace the "YOUR-GITHUB-USERNAME" with your actual username for github, in my case the line becomes ``git remote add origin git@github.com:punteney/tutorial_zoo_site.git``

To make pushing and pulling from github a little easier add the following to the bottom of the ~/projects/zoo/zoo/.git/config file::

    [branch "master"]
        remote = origin
        merge = refs/heads/master

With this specified in the git config file the ``gps origin master`` command above becomes just ``gps`` as it now knows the default remote for the 'master' branch is 'origin' which was already defined in the config file.

At this point it's a default working project that you can build out from.
