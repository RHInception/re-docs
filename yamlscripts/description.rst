.. _yaml_description:

Description
^^^^^^^^^^^

Finally, an execution step **may** define a optional ``description``
item.

* **Required**: No
* **Argument Type**: ``string``
* **Default**: None

The ``description`` element allows you to provide a useful
human-readable description of what the step is exactly supposed to
do. Use of ``description`` items are **encouraged**.

Recall our previous example of using ``service.Restart`` to restart
the ``megafrobber`` service. Below, line **5**, shows an example of
how to use the ``description`` item.

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 5

   ---
   group: inception
   name: Simple playbook
   execution:
     - description: Restart the megafrobber service
       hosts:
         - foo.bar.example.com
       steps:
         - service.Restart: {service: megafrobber}


Because this item is optional, we could just as well have omitted it entirely:

.. code-block:: yaml
   :linenos:

   ---
   group: inception
   name: Simple playbook
   execution:
     - hosts:
         - foo.bar.example.com
       steps:
         - service.Restart: {service: megafrobber}
