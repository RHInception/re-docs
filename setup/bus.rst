.. _setting_up_the_bus:

The Bus
~~~~~~~
Release Engine requires an AMQP service allowing messages to pass between components. The current, verified to work, AMQP service used with Release Engine is `RabbitMQ <http://www.rabbitmq.com/>`_, an `Erlang-based <http://www.erlang.org/>`_ open source messaging service. For more information on setting up a RabbitMQ server please read the project's `server documentation <http://www.rabbitmq.com/admin-guide.html>`_.

For security best practices, each component that transmits on the bus should have it's own username and password combination. By enforcing component username/passwords access can be restricted to just what a component needs. This also allows quick deactivation of a component in the event something goes terribly wrong or a service is compromised.

Setup Steps
```````````

.. note::
   Provision or utilize an existing server to install RabbitMQ or similar AMQP compliant service.  For the rest of this article we will assume that you are running the service on RabbitMQ.

* Install `RabbitMQ Server <http://www.rabbitmq.com/install-rpm.html>`_
* Open ports **5672** (*AMQP*) and **15672** (*management*)
* Enable RabbitMQ management via the `Management Plugin <http://www.rabbitmq.com/management.html>`_
* Start RabbitMQ
* Create an exchange called "re" using topics
* Create a user for RE-REST (the rest interface into Release Engine)
* Create a user for RE-CORE (the state machine)
* Create a queue for RE-CORE
* Bind the RE-CORE queue to the re exchange with job.create
* Create a user for each component your instance will support
* Create a queue for each component your instance will support
* Bind the queue for each additional component (not including RE-REST and RE-CORE which are mandatory and separate from any additional components) to the re exchange with connectors that describe the step or plugin that your instance will support.

.. todo::
   List binding instructions for queues


Test Setup
``````````
.. todo::
   How to verify it's ready.
