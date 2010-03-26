Local Dev Setup
================
For my local development environment I use a Mac with OS X 10.6, the requirements and setup will be based on that.

Prerequisites
------------------
It's assumed that you already have these items installed:

Python
^^^^^^^^^^^^^^^
`Python <http://python.org>`_ 2.4 or higher, which is installed on OS X by default. To verify go to the terminal and on the command line type ``python`` to enter the python interactive shell. You should see output along these lines (The first line tells you what version is installed, in this case Python 2.5.4)::

Python 2.5.4 (r254:67916, Jul  7 2009, 23:51:24) 
[GCC 4.2.1 (Apple Inc. build 5646)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> 

The first line tells you what python version is installed, in this case Python 2.5.4. To exit out of the interactive shell type ``ctrl d``

Text Editor or IDE
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
There are many text editors and IDE's that can be used for Python and Django. I use `Textmate <http://macromates.com/>`_ but any editor that you are comfortable with should work.

Version and Source Control
----------------------------------
Version control will be done using `git <http://git-scm.com/>`_ on `github <http://github.com>`_.

First signup for a `github <http://github.com>`_ account if you don't have one already.  Once you have the github account follow their `instructions <http://help.github.com/git-installation-redirect>`_ unless you have a specific reason not to I'd recommend using the Pre-compiled installer for OS X as that is the easiest install method.

.. admonition:: Other Version Control Systems

    There are many other version control systems out there. The two most common that you will run into while doing Django and Python work are:
    
    * `Subversion <http://subversion.tigris.org/>`_ which is what the Django project uses for source control.
    * `Mercurial <http://mercurial.selenic.com/>`_ (sometimes referred to as "hg") a distributed version control system written in Python.
    
    Neither Subversion or Mercurial are needed for this tutorial at somepoint you will probably need to install and interact with them.

PostgreSQL Database
--------------------------
`PostgreSQL <http://www.postgresql.org/>`_ will be the database for the project. An `OS X binary install file is available <http://www.postgresql.org/download/macosx>`_, install it with the default options. As part of the installation `pgAdmin <http://www.pgadmin.org/>`_ is installed, this is a nice gui application that will be used for managing the postgres databases.

.. admonition:: Other Database System

    Django is fairly agnostic about the database system it uses, so other databases than Postgres can be used. Many develop locally with `sqlite <http://www.sqlite.org/>` for it's ease of setup and then use PostgreSQL, MySQL, Oracle, etc. for production purposes. I use Postgres for development to minimize the differences between the production and dev environment to hopefully find any database specific issues earlier in the process.


Pip and Virtualenv
---------------------------
`Pip <http://pip.openplans.org/>`_ is great for installing python packages and works well with virtualenv for localized package installations. To install pip run the following from the command line.::

    sudo easy_install pip

`Virtualenv <http://pypi.python.org/pypi/virtualenv>`_ is a tool to create isolated Python environments. This is used to isolate different projects from each other. For example say one project requires Django 1.0 and another requires Django 1.1 virtualenv makes it easy to seperate those projects so one can use Django 1.0 and the other can use Django 1.1 without any need for messing with the python path (trust me it makes life much easier). To install virtualenv run::

    sudo pip install virtualenv

`Virtualenvwrapper <http://www.doughellmann.com/projects/virtualenvwrapper/>`_ is  helper app that make it easier to use virtualenv. To install virtualenvwrapper run the following commands::

    sudo pip install virtualenvwrapper
    echo "export WORKON_HOME=$HOME/.virtualenvs" ~/.bashrc
    echo "source /usr/local/bin/virtualenvwrapper_bashrc" ~/.bashrc
    source ~/.bashrc

To verify it is installed from the run ``workon`` from the commandline **TODO PUT THE OUTPUT OF WORKON HERE**

Shortcuts, Helper scripts, and Aliases
-----------------------------------------------
Items to minimize repetitive tasks and keystrokes.

Project location
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Keeping all your projects in the same location allows for easier automation as an assumption can be made as to the location of the projects. For this tutorial we'll use a "projects" folder created within your home directory. To create the folder run the following:: 

    mkdir ~\projects

Aliases
^^^^^^^^^^^^^^^^^^^^^
Using aliases common commands can be shortened to save some typing and speed up the process. It will be assumed that you have these aliases created for use in this tutorial.

Git Aliases
""""""""""""""""""""
In general git commands take the format of ``git push`` and ``git commit`` etc. Some git commands are used very frequently while working on a project, by creating aliases for these common commands it saves a little time. Add the bottom of the ~/.bashrc file add these lines::

    alias gcm="git commit"
    alias gpl="git pull"
    alias gps="git push"
    alias gpsa="git push --all"
    alias ga="git add"
    alias gst="git status"
    alias gdf="git diff"
    alias gdiff="git diff"

With these aliases now instead of typing out ``git commit`` or ``git add`` you just type ``gcm`` or ``ga`` respectively. It's a small difference but feels much quicker.

Helper Scripts
^^^^^^^^^^^^^^^^^^^^^
The virtualenv wrapper provides a lot of helpful functionality for working with virtualenvs, one of the items it provides is the ability to easily hook in scripts that run at specific points in the virtualenv lifecycle. I've created some quick scripts using these hooks that will do a couple of things; 

* When a new virtual environment is created a directory with a matching name will be created in the projects folder (if it doesn't exist already).
* When a virtual environment is activated it will automatically change to it's corresponding project directory and it will add the project bin folder on to the path environment variable.

To install the scripts::

    cd ~/projects/
    git clone git://github.com/punteney/virtualenv-scripts.git
    virtualenv-scripts/install.sh

"git clone" makes a local copy of the repository in the folder ~/projects/virtualenv-scripts."virtualenv-scripts/install.sh" script creates symbolic links to the scripts in ~/projects/virtualenv-scripts/global_scripts in the ~/.virtualenvs folder. In the future if new updates are made to the scripts all you need to do to get the updates is::

    cd ~/projects/virtualenv-scripts
    gpl

Where "gpl" is the aliases we created above for "git pull".

