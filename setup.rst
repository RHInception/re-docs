.. _setting_up:

Setting Up
==========

Infrastructure Requirements
----------------------------

.. _setting_up_the_bus:

The Bus
~~~~~~~
Release Engine requires an AMQP service allowing messages to pass between components. The current, verified to work, AMQP service used with Release Engine is `RabbitMQ <http://www.rabbitmq.com/>`_, an erlang based open source messaging service. For more information on setting up a RabbitMQ server please read the project's `server documentation <http://www.rabbitmq.com/admin-guide.html>`_.

For security best practices, each compoenent that transmits on the bus should have it's own username and password combination. By enforcing component username/passwords access can be restricted to just what a component needs. This also allows quick deactivation of a component in the event something goes terribly wrong or a service is compromised.

Setup Steps
~~~~~~~~~~~

.. note::
   Provision or utilize an existing server to install RabbitMQ or similar AMQP compliant service.  For the rest of this article we will assume that you are running the service on RabbitMQ.

* Install RabbitMQ server
* Open ports 5672 (AMQP) and 15672 (management)
* Enable RabbitMQ management via the `Management Plugin <http://www.rabbitmq.com/management.html>`_
* Start RabbitMQ
* Create an exchange called "re" using topics
* Create a user for RE-REST (the rest interface into Release Engine)
* Create a user for RE-CORE (the state machine)
* Create a queue for RE-CORE
* Bind the RE-CORE queue to the re exchange with job.create
* Create a user for each component your instance will support
* Create a queue for each component your instance will support
* Bind the queue for each additional component (not including RE-REST and RE-CORE which are manditory and separate from any additional components) to the re exchange with connectors that describe the step or plugin that your instance will support.

.. todo::
   List binding instructions for queues


Test Setup
``````````
.. todo::
   How to verify it's ready.


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

Core Component Requirements
---------------------------
There are two components you must have no matter what workers you choose to support. These compoenents are: `RE-REST <https://github.com/RHInception/re-rest/>`_ and `RE-CORE <https://github.com/RHInception/re-core>`_.

RE-REST
~~~~~~~
RE-REST is the REST endpoint for interacting with the Release Engine. This is the only interaction point by design. RE-REST is a `Flask <http://flask.pocoo.org/>`_ based application and requires a few libraries before it will work properly.

Setup Steps
```````````
* Provision or choose a server to utilize for RE-REST
* Install Python v2
* Install the python v2 libraries `listed here <https://github.com/RHInception/re-rest/blob/master/requirements.txt>`_.
* Follow the RE-REST configuration instruction at :ref:`rerest-conf`.
* Choose and implement a RE-REST deployment strategy via :ref:`rerest-deployment`.

Test Setup
``````````
.. todo::
   How to verify it's ready.


RE-CORE
~~~~~~~
The core is essentially a finite state machine (FSM) hooked into a message bus and a database.

The core oversees the execution of all release steps for any given project. The core is separate from the actual execution of each release step. Execution is delegated to the worker components.

Setup Steps
```````````
* Provision or choose a server to utilize for RE-CORE
* Install Python v2
* Install the python v2 libraries `listed here <https://github.com/RHInception/re-core/blob/master/requirements.txt>`_.
* Follow the RE-CORE configuration instructions at :ref:`recore-conf`.
* Choose and implement a RE-CORE deployment strategy via :ref:`recore-deployment`.

Test Setup
``````````
.. todo::
   How to verify it's ready.
