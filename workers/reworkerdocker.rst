RE-WORKER-DOCKER
----------------
Release Engine Worker Plugin that handles basic Docker control functions.


This worker takes two configuration files. The first is the Docker configuration file. It should look like this:

.. code-block:: json

  {
    "queue": "docker",
    "version": "1.16"
  }


The version listed is the version of the docker API that is in use.


This worker takes the normal MQ configuration as it's only configuration file:

.. code-block:: json

  {
    "server": "127.0.0.1",
    "port": 5672,
    "vhost": "/",
    "user": "guest",
    "password": "guest"
  }

* Set the ``MQ config file`` parameters to sane values (see also:
  :ref:`Setting Up The Bus<setting_up_the_bus>`)
* Run the worker: ``re-worker-docker -w $YOUR_WORKER_CONFIG $YOUR_MQ_CONF.json``

We should see output similar to the following if everything well:

.. code-block:: bash

   [user@frober re-worker-docker]$ re-worker-docker -w docker.json mq.json
   2014-05-19 14:39:47,080 - DockerWorker - WARNING - No app logger passed in. Defaulting to Streamandler with level INFO.
   2014-05-19 14:39:47,083 - DockerWorker - INFO - Attempting connection with amqp://inceptadmin:***@messagebus.example.com:5672/
   2014-05-19 14:39:47,412 - DockerWorker - INFO - Connection and channel open.
   2014-05-19 14:39:47,413 - DockerWorker - INFO - Consuming on queue worker.docker

Commands
^^^^^^^^

The Docker Worker steps are documented in :ref:`Worker Steps: Docker <steps_docker>`.
