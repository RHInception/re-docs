.. _the_datastore:

The Datastore
~~~~~~~~~~~~~
Release engine utilizes `MongoDB <http://www.mongodb.org/>`_ for storing playbooks and other persistent data. Authentication must be turned on and it's highly recommended to create a username/password for every component that requires access to the data store.


Setup Steps
```````````

.. note::
   Provision or utilize an existing server to install MongoDB or similar service like Amazon DynamoDB for the NoSQL service.  For the rest of this article we will assume that you are running a local MongoDB service.

* Provision or choose a server to utilize for the datastore
* Install MongoDB on the server
* Open port 27017
* Update MongoDB for authentication (see `the documentation <http://docs.mongodb.org/manual/tutorial/enable-authentication/>`_)
* Start MongoDB
* Create a database called "re"
* Create a user for RE-CORE on database "re"
* Create a user for RE-REST on database "re"
* Import the initial data for the database via MongoDB Command Line Tools or one of the `many MongoDB UI Tools <http://mongodb-tools.com/>`_.

.. todo::
   Provide a link to the initial database import

Test Setup
``````````
.. todo::
   How to verify it's ready.
