Pushing the Code Live
**************************
The instructions are for pushing the project onto a fresh Ubuntu 10.04 server. If using a different, relatively recent, version of Ubuntu it should still work, though it won't have been tested. If using a different linux variant it definitely won't work, but could be made to work with a few changes to use the distribution specific package manager and for different default file locations.

These instructions are for a single server using Nginx as the front proxy and static file server, Apache2 with mod_wsgi for serving the Django project, and a Postgresql database.




Production
======================

Server Setup
----------------------------------------
First step is to setup a server with Ubuntu 9.10 on it. This can be a physical server you own or a virtual server through somebody like `Slicehost <http://www.slicehost.com/>`_, `Rackspace Cloud <http://rackspacecloud.com>`_, of any other virtual server provider. For testing Rackspace Cloud was used.

Once the server is provisioned and ready to go ssh/login as root (Rackspace will send the IP and root password via the email on your account).::

    ssh root@IP_ADDRESS 

Once logged in the first thing to do is to change the root password as it was sent in plain text via email::

    passwd
    
And when prompted enter a new password, and make sure to remember it for future use. (For the Mac `1password <http://agilewebsolutions.com/products/1Password>`_ is a good app for storing passwords)

The second step is to install a firewall. Ubuntu provides the `Uncomplicated Firewall <https://help.ubuntu.com/community/UFW>`_ (ufw) for managing the iptables firewall. If you have very complex firewall needs you may need to work with iptables directly. For most cases ufw will make configuring the firewall much easier and straightforward. To configure the firewall::

    aptitude install ufw
    ufw default deny
    ufw allow ssh/tcp
    ufw enable
    ufw allow 80

The first line installs ufw, the second line says by default we want to block everything coming in. The third line enables ssh access on the default port of 22. The fourth line enables the firewall. The last line opens up port 80 for http website traffic. If you need ssl access to the site you'll want to run the command ``ufw allow 443``.

.. note::

    Apache will be set to use port 8080. For testing and debugging it can sometimes be helpful to have this port open so you can access apache directly without the nginx proxy. To set this use the following command ``ufw allow 8080``.

Now that the root password is changed and the firewall is enabled it's time to create the project user. This user should be specific to the project and not an actual user. For this tutorial the user will be 'zoo'. To create the user::

    adduser zoo

Fill in the additional information it asks for and make sure to remember the password entered.

.. note:: 

    The reason the user should be a generic user specific to the project is that the project files are kept in the created user's home directory. If a user is created who is an actual user then others working on the project would need to have access to the that users home directory, and permissions could get a bit complicated.

Now the 'zoo' user needs to be give permission to 'sudo' to be able to install system wide packages and restart processes. To do this type the following command on the server::

    visudo

This will open the '/etc/sudoers' file in the nano text editor for editing. Scroll down in the file until you see a line that reads ``USERNAME   ALL=(ALL) ALL`` right below that add the following line ``zoo   ALL=(ALL) ALL`` then close the file by hitting ctrl-x and saving the changes.

.. note::

    The above commands can be used to create additional users, such as one for yourself. This isn't required but depending on how the server will be used can be useful.

Project Configuration
---------------------------
From this point on most of the server installation and configuration will be done through fabric. In order for that to work the various configuration files must be updated.

Updating in the config files
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Most of the changes here are to set the path to files properly. Where ever there is a 'USERNAME' replace it with 'zoo' where ever there is a 'PROJECT_NAME' replace it with 'zoo_site'.

* zoo_site/config/apache/production/django_site.conf
    * WSGIScriptAlias - set it to ``WSGIScriptAlias / /home/zoo/zoo_site/live/config/wsgi/production/django_site.py``
* zoo_site/config/nginx/production/django_site.conf
    * In the ``location ~ ^/(favicon.ico|robots.txt|sitemap.xml)`` directive set the root to be ``root /home/zoo/zoo_site/live/project/media;``
    * In the ``location /media`` directive set the root to be ``root /home/zoo/zoo_site/live/project/media;``

.. note::

    A global search and replace on the config directory for '/USERNAME/PROJECTNAME/' and replace with '/zoo/zoo_site/' will work for the above changes.
    
Updating the production settings file
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The following items need to be set in the project/settings/production.py file

* DATABASE_NAME - set it to 'zoo_prod'
* DATABASE_USER - set it to 'zoo'
* MEDIA_URL - set it to '/media/'

The DATABASE_PASSWORD doesn't need to be set as Postgres will be set to trust local connections.

Updating the fabfile
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The fabfile.py (in the root of the 'zoo_site' directory) is currently empty. Copy the contents of `default_fabfile.py <http://github.com/punteney/fabric_helpers/blob/master/default_fabfile.py>`_ into the fabfile.py. Once copied over change these settings:

* USER - set it to 'zoo' (this needs to match the username used in the config files)
* env.project_name - set it to 'zoo_site' (this needs to match the project name used in the config files)
* MACHINES - in the machine line replace the 'SERVER IP OR HOSTNAME HERE' with the created servers public IP address or the hostname for the server.
* env.git_repo - the git repo path from github

Pushing project changes to github
--------------------------------------
Now that the files have been updated we need to push them to github so they will be pulled down onto the production server::

    ga *
    gcm -m "Updating the fabfile, settings and config files for production"
    gps


Server Configuration
---------------------------
To start the server configuration issue the following command locally::

    workon zoo
    fab production initial_install

This will install all the needed software, configure the server software, setup the virtual environment, and project.

.. note::

    If using a private github repository ssh keys will need to be installed to allow the server access to the git repository. Generally this is best accomplished using the github's deploy keys. Ssh back into the server as the 'zoo' user and then follow these instructions to generate the keys http://help.github.com/key-setup-redirect 
    
    Once the keys are created copy the contents of the public key file ``~/.ssh/id_rsa.pub``. Login to your github account go to the admin section of the repository and select the "deploy keys" and paste the public key in there.


Database Creation
-------------------------
Ssh back into the server ``ssh zoo@SERVER_IP_ADDRESS`` (or use the existing connection if it's still open) and issue the following commands to create the database user and name::

    sudo -u postgres createuser zoo
    sudo -u postgres createdb -O zoo zoo_prod

The first line creates a user in postgres called 'zoo'. This command will ask you about additional permissions to give this user, in general I recommend answering no to them. The second line creates the database 'zoo_prod' and sets it to be owned by the just created 'zoo' user. 

Pushing the project
--------------------------
To push the project live issue the following command from your local system::

    workon zoo
    fab production push
    
This will update the project and any requirements, which at this point there shouldn't be any changes, and then run syncdb to create the database tables.


Creating a Django superuser
-----------------------------------
Ssh into the server as the zoo user ``ssh zoo@SERVER_IP_ADDRESS`` and run the following commands to create a superuser for your django project::

    workon master
    cd ~/zoo_site/live/project/
    ./manage.py createsuperuser

Answer the questions it asks for creating the superuser.

Access the admin
--------------------
At this point the project should be working. To test go to http://SERVER_IP_O_HOSTNAME/admin/ and is should show you the login form for the admin. Login using the superuser you created above.



Pushing Ongoing changes
-----------------------------
There are two primary commands for pushing ongoing changes 'push' and 'push_quick'.

* **push**, as mentioned above, will update all the project requirements installing newer versions and adding any new modules, it will also run the syncdb command updating the database with any new tables. Then finally restart the servers (not the physical servers, but the software servers, ).
* **push_quick** will pull the newest version of the project code, but does not try to install or upgrade the project requirements or run syncdb. Once completed it also only reloads the servers that need to be reloaded for a code change.

If new requirements have been added to the project or there are new apps/database tables, or if I'm unsure if there have been changes I will use 'push'. Otherwise, I use push_quick as it's quicker and the server restarts are a bit quicker and cleaner.

Test
^^^^^^^^^^^^^^^
TBD

Staging
^^^^^^^^^^^^^^^
TBD



Fabric
------------

Fabric Helpers
^^^^^^^^^^^^^^^^^^^^^^

Fabric File Setup
^^^^^^^^^^^^^^^^^^^^^^^^^^

Required Packages
-------------------------

Python Packages
^^^^^^^^^^^^^^^^

OS Packages
^^^^^^^^^^^^^^^^^^^

Initial Setup of Servers
-----------------------------


Pushing Changes
----------------------

Rolling back and Reverting
-----------------------------


