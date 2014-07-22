.. _steps_servicenow:

ServiceNow
**********

servicenow:DoesChangeRecordExist
================================

Checks to see if a change record exists. Resulting data will have an exists key with a bool.

**Dynamic Arguments**

* ``change_record`` (type ``str``)

  * **Required:** True
  * **Description:** The change record to look for.

**Example**

To check if a change record exists:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3,4

   hosts: ['localhost']
   steps:
       - servicenow:DoesChangeRecordExist:
           dynamic:
               - change_record

.. note:: This check is ran for each host in ``hosts``

.. note:: This step has no direct side-effects. It is more useful as a :ref:`Pre-Deployment Step <components_recore_predeployment_checks>`
