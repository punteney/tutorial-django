Local Dev Setup
================
For my local development environment I use a Mac with OS X 10.6, the requirements and setup will be based on that.

Prerequisites
------------------
It's assumed that you already have these items installed:

Python
^^^^^^^^^^^^^^^
`Python <http://python.org>`_ 2.4 or higher, which is installed on OS X by default. To verify go to the terminal and on the command line type ``python`` to enter the python interactive shell. You should see output along these lines::

    Python 2.5.4 (r254:67916, Jul  7 2009, 23:51:24) 
    [GCC 4.2.1 (Apple Inc. build 5646)] on darwin
    Type "help", "copyright", "credits" or "license" for more information.
    >>> 

The first line tells you what python version is installed, in this case Python 2.5.4. To exit out of the interactive shell hit ``ctrl d``

Text Editor or IDE
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
There are many text editors and IDE's that can be used for Python and Django. I use `Textmate <http://macromates.com/>`_ but any editor that you are comfortable with should work.

Version and Source Control
----------------------------------
Version control will be done using `git <http://git-scm.com/>`_ on `github <http://github.com>`_. 

To install git follow these steps:

1. Signup for a `github <http://github.com>`_ account if you don't have one already.  
2. Follow github's `instructions <http://help.github.com/git-installation-redirect>`_ for installing git. Unless you have a specific reason not to I'd recommend using the pre-compiled installer for OS X as that is the easiest install method. 
3. Once git is is installed follow the github instructions for `generating SSH keys <http://help.github.com/mac-key-setup/>`_

.. admonition:: Other Version Control Systems

    There are many other version control systems out there. The two most common that you will run into while doing Django and Python work are:
    
    * `Subversion <http://subversion.tigris.org/>`_ which is what the Django project uses for source control.
    * `Mercurial <http://mercurial.selenic.com/>`_ (sometimes referred to as "hg") a distributed version control system written in Python.
    
    Neither Subversion or Mercurial are needed for this tutorial, but at somepoint you will probably need to install and interact with them.

PostgreSQL Database
--------------------------
`PostgreSQL <http://www.postgresql.org/>`_ will be the database for the project. An `OS X binary install file is available <http://www.postgresql.org/download/macosx>`_, install it with the default options. 

Once the installation has finished edit the '/Library/PostgreSQL/8.4/data/pg_hba.conf' file (you'll need to do this using 'sudo' to have the rights to edit the file ``sudo nano /Library/PostgreSQL/8.4/data/pg_hba.conf``). Scroll down towards the bottom of the file until you see these lines::

    # "local" is for Unix domain socket connections only
    local   all         all                               md5
    # IPv4 local connections:
    host    all         all         127.0.0.1/32          md5

Change those line so instead of requiring md5 passwords they are trusted connections::

    # "local" is for Unix domain socket connections only
    local   all         all                               trust
    # IPv4 local connections:
    host    all         all         127.0.0.1/32          trust

Once that file is changed start the PostGres Server. This can be done through the "start server.app" in the "Postgres 8.4" folder in the "Applications" folder.

.. admonition:: Database Security

    As this database is just a dev database running locally it's setup without a password allowing anyone with access to the local system to access the database. In most cases this level of security is fine, if more security is needed adjust the pg_hba.conf file to meet the required security needs (`postgres authentication docs <http://www.postgresql.org/docs/8.4/interactive/client-authentication.html>`_).

Finally it's helpful to tell the system where to find the PostgreSQL executable files by adding them to the PATH variable::

    echo "export PATH=$PATH:/Library/PostgresPlus/8.4/bin/" ~/.bashrc
    PATH=$PATH:/Library/PostgresPlus/8.4/bin/

Note the above lines are for Postgres 8.4 if installing a different version of Postgres make sure to use the correct version number.

As part of the installation `pgAdmin3 <http://www.pgadmin.org/>`_ is installed, this is a gui application that is helpful for managing the postgres databases.

.. admonition:: Other Database System

    Django is fairly agnostic about the database system it uses, so other databases than Postgres can be used. Many develop locally with `sqlite <http://www.sqlite.org/>`_ for it's ease of setup and then use PostgreSQL, MySQL, Oracle, etc. for production purposes. I use Postgres for development to minimize the differences between the production and dev environment to hopefully find any database specific issues earlier in the process.


Pip and Virtualenv
---------------------------
`Pip <http://pip.openplans.org/>`_ is great for installing python packages and works well with virtualenv for localized package installations. To install pip run the following from the command line.::

    sudo easy_install pip

`Virtualenv <http://pypi.python.org/pypi/virtualenv>`_ is a tool to create isolated Python environments. This is used to isolate different projects from each other. For example say one project requires Django 1.0 and another requires Django 1.1 virtualenv makes it easy to seperate those projects so one can use Django 1.0 and the other can use Django 1.1 without any need for messing with the python path (trust me it makes life much easier). To install virtualenv run::

    sudo pip install virtualenv

`Virtualenvwrapper <http://www.doughellmann.com/projects/virtualenvwrapper/>`_ is  helper app that make it easier to use virtualenv. To install virtualenvwrapper run the following commands::

    sudo pip install virtualenvwrapper
    mkdir ~/.virtualenvs
    echo "export WORKON_HOME=$HOME/.virtualenvs" >> ~/.bashrc
    echo "source /usr/local/bin/virtualenvwrapper_bashrc" >> ~/.bashrc
    source ~/.bashrc

To verify it is installed from the run ``workon`` from the command line. It should run and show an output of ``*``.

Shortcuts, Helper scripts, and Aliases
-----------------------------------------------
Items to minimize repetitive tasks and keystrokes.

Project location
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Keeping all your projects in the same location allows for easier automation as an assumption can be made as to the location of the projects. For this tutorial we'll use a "projects" folder created within your home directory. To create the folder run the following:: 

    mkdir ~/projects

Git Shortcuts
^^^^^^^^^^^^^^^^^^^^^^^^
In general git commands take the format of ``git push`` and ``git commit`` etc. Some git commands are used very frequently while working on a project, by creating aliases for these common commands it saves a little time. Add the following to the bottom of the ~/.bashrc file::

    alias gcm="git commit"
    alias gpl="git pull"
    alias gps="git push"
    alias gpsa="git push --all"
    alias ga="git add"
    alias gst="git status"
    alias gdf="git diff"
    alias gdiff="git diff"

With these aliases now instead of typing out ``git commit`` or ``git add`` you just type ``gcm`` or ``ga`` respectively. It's a small difference but they are commands that are typed often.

Setting git to ignore some file types by default helps keeps the repositories clean. To ignore .pyc and .DS_Store files for all your repositories on the local system run the following commands::

    echo "*.DS_Store" >> ~/.gitignore
    echo "*.py[c|o]" >> ~/.gitignore


Helper Scripts
^^^^^^^^^^^^^^^^^^^^^
The virtualenv wrapper provides a lot of helpful functionality for working with virtualenvs, one of the items it provides is the ability to easily hook in scripts that run at specific points in the virtualenv lifecycle. I've created some quick scripts using these hooks that will do a couple of things; 

* When a new virtual environment is created a directory with a matching name will be created in the projects folder (if it doesn't exist already).
* When a virtual environment is activated it will automatically change to it's corresponding project directory and it will add the project bin folder on to the path environment variable.

To install the scripts::

    cd ~/projects/
    git clone git://github.com/punteney/virtualenv-scripts.git
    virtualenv-scripts/install.sh

"git clone" makes a local copy of the repository in the folder ~/projects/virtualenv-scripts. The "virtualenv-scripts/install.sh" script creates symbolic links to all the scripts in ~/projects/virtualenv-scripts/global_scripts in the ~/.virtualenvs folder. In the future if new updates are made to the scripts all you need to do to get the updates is::

    cd ~/projects/virtualenv-scripts
    gpl origin master

Where "gpl" is the aliases we created above for "git pull".


