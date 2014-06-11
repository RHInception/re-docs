re-rest ↔ re-core
-------------------


re-rest produces two formats: :ref:`Deployment Message Format
<deployment_message_format>` and :ref:`Notification Message Format
<notification_message_format>`. It also receives on message format in
return: :ref:`Deployment Response Message Format
<deployment_response_message_format>`.


Deployment Message Format
~~~~~~~~~~~~~~~~~~~~~~~~~
.. _deployment_message_format:

The Deployment Message format comes in two flavors, Simple and Dynamic.


Simple Deployment Message Format
````````````````````````````````
The simple format has two required keys: ``group`` and ``playbook_id``.

**Required Keys**:

* group: the group's name as a string
* playbook_id: the specific playbook in the group to utilize


**Example**:

.. code-block:: json

   {
      "group": "My Group",
      "playbook_id": "1234567890"
   }


Dynamic Deployment Message Format
`````````````````````````````````
The dynamic format adds a key to the simple format: dynamic.

.. note::

   For more information on dynamic variables see :ref:`Dynamic Variables <rerest_dynamic_variables>`.

**Required Keys**:

* group: the group's name as a string
* playbook_id: the specific playbook in the group to utilize
* dynamic: a JSON object holding key/values for specific workers


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

**Required Keys**:

* id: the deployment id or null


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
