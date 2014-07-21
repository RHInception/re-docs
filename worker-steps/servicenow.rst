.. _steps_servicenow:

ServiceNow
**********

servicenow:DoesChangeRecordExist
================================

Checks to see if a change record exists. Resulting data will have an exists key with a bool.

**Parameters**

* ``change_record`` (type ``str``)

  * **Required:** True
  * **Description:** The change record to look for.

**Example**

To check if a change record CHG00000 exists:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3,4

   hosts: ['localhost']
   steps:
       - servicenow:DoesChangeRecordExist:
           change_record: CHG000000

.. note:: If more than one host is given in ``hosts``, the playbook will check again for each host given.
