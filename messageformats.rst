Message Formats
===============

re-rest <-> re-core
-------------------
re-rest produces two formats: :ref:`Deployment Message Format <deployment_message_format>` and :ref:`Notification Message Format <notification_message_format>`. It also receives on message format in return:  :ref:`Deployment Response Message Format <deployment_response_message_format>`.


Deployment Message Format
~~~~~~~~~~~~~~~~~~~~~~~~~
.. _deployment_message_format:

The Deployment Message format comes in two flavors, Simple and Dynamic.


Simple Deployment Message Format
````````````````````````````````
The simple format has two keys: group and playbook_id. group denotes the group's name and
playbook_id denotes the specific playbook in the group to utilize. These two keys must
always be present for a deployment to start!


**Example**:

.. code-block:: json

   {
      "group": "My Group",
      "playbook_id": "1234567890"
   }


Dynamic Deployment Message Format
`````````````````````````````````
The dynamic format adds a key to the simple format: dynamic. The dynamic key is a JSON object
which holds key/values which are needed by specific workers. For more information on dynamic
variables see :ref:`Dynamic Variables <rerest_dynamic_variables>`.

**Example**:

.. code-block:: json

   {
      "group": "My Group",
      "playbook_id": "1234567890",
      "dynamic": {
          "cart": "my cart",
          "environment": "QA"
      }
   }



Deployment Response Message Format
``````````````````````````````````
.. _deployment_response_message_format:

This format is sent from re-core back to re-rest which tells re-rest if the deployment was accepted or not.
The message has one key: id. If the deployment was able to start the id will have a deployment id in it. If
there was an issue starting a deployment, for example, because the group or playbook doesn't exist, the id
will be null.

**Example of a Deployment Successfully Starting**:

.. code-block:: json

   {
      "id": "9999999999999999"
   }

**Example of a Deployment Failing to Start**:

.. code-block:: json

   {
      "id": null
   }


re-core <-> re-workers
----------------------



Notification Message Format
---------------------------

.. _notification_message_format:
