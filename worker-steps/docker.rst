.. _steps_docker:

Docker
******

docker:StopContainer
====================

Stops a running container.

**Parameters**

* ``server_name`` (type ``str``)

  * **Required:** True
  * **Description:** The name of the server to connect to.


* ``container_name`` (type ``str``)

  * **Required:** True
  * **Description:** The container to stop.


**Example**

To stop the container ``mycontainer`` on ``127.0.0.1``:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3-5

   hosts: ['localhost']
   steps:
       - docker:StopContainer:
           server_name: "127.0.0.1:2376"
           container_name: "mycontainer"


docker:RemoveContainer
======================

Removes a container.

**Parameters**

* ``server_name`` (type ``str``)

  * **Required:** True
  * **Description:** The name of the server to connect to.


* ``container_name`` (type ``str``)

  * **Required:** True
  * **Description:** The container to remove.


**Example**

To remove the container ``mycontainer`` on ``127.0.0.1``:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3-5

   hosts: ['localhost']
   steps:
       - docker:RemoveContainer:
           server_name: "127.0.0.1:2376"
           container_name: "mycontainer"

docker:PullImage
================

.. note::
   Currently the docker worker only pulls images from a given insecure registry. In the future this will be changed to allow for secure or insecure.

Pulls an image down to a server.

**Parameters**

* ``server_name`` (type ``str``)

  * **Required:** True
  * **Description:** The name of the server to connect to.

* ``image_name`` (type ``str``)

  * **Required:** True
  * **Description:** The name of the image to pull.

* ``insecure_registry`` (type ``str``)

  * **Required:** True
  * **Description:** The name the insecure registry to pull from.



**Example**

To pull the image ``myimage`` to ``127.0.0.1`` from ``http://registry.example.com``:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3-6

   hosts: ['localhost']
   steps:
       - docker:RemoveContainer:
           server_name: "127.0.0.1:2376"
           image_name: "myimage"
           insecure_registry: "http://registry.example.com"


docker:CreateContainer
======================

Creates a container for use.

**Parameters**

* ``server_name`` (type ``str``)

  * **Required:** True
  * **Description:** The name of the server to connect to.

* ``image_name`` (type ``str``)

  * **Required:** True
  * **Description:** The name of the image to pull.

* ``container_name`` (type ``str``)

  * **Required:** True
  * **Description:** The name to use for the new container.

* ``container_command`` (type ``str``)

  * **Required:** True
  * **Description:** The command used when starting the new container.

* ``container_hostname`` (type ``str``)

  * **Required:** False
  * **Description:** The hostname of the new container.

* ``container_ports`` (type ``list``)

  * **Required:** False
  * **Description:** The port setup for the new container.


**Example**

To pull the image ``myimage`` to ``127.0.0.1`` from ``http://registry.example.org``:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3-9

   hosts: ['localhost']
   steps:
       - docker:RemoveContainer:
           server_name: "127.0.0.1:2376"
           image_name: "myimage"
           container_name: "mycontainer"
           container_command: "/use/bin/supervisord"
           container_hostname: "container.example.org"
           container_ports: [1111, 2222]


docker:StartContainer
=====================

Starts a container.

**Parameters**

* ``server_name`` (type ``str``)

  * **Required:** True
  * **Description:** The name of the server to connect to.

* ``container_name`` (type ``str``)

  * **Required:** True
  * **Description:** The name to use for the new container.

* ``container_binds`` (type ``dict``)

  * **Required:** False
  * **Description:** The file system binds.

* ``port_bindings`` (type ``dict``)

  * **Required:** False
  * **Description:** The mapping of host and container ports.


**Example**

To start the container ``mycontainer`` on ``127.0.0.1`` binding ``1111`` to the hosts port of ``8080`` and binding the hosts ``/var/data/`` to the image at ``/var/www/html/`` as read-only:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3-10

   hosts: ['localhost']
   steps:
       - docker:RemoveContainer:
           server_name: "127.0.0.1:2376"
           container_name: "mycontainer"
           container_binds:
               "/var/data/":
                   "bind": "/var/www/html"
                   "ro": True
           port_bindings: {1111: 8080}
