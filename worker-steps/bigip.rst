.. _steps_bigip:

BigIP
*****


bigip:InRotation
================

Enable the current host in the BigIP.

**Example**

.. code-block:: yaml
   :linenos:

   hosts: ["example01.com", "example02.com"]
   steps:
       - bigip:InRotation




bigip:OutOfRotation
===================

Disable the current host in the BigIP.

**Example**

.. code-block:: yaml
   :linenos:

   hosts: ["example01.com", "example02.com"]
   steps:
       - bigip:OutOfRotation



bigip:ConfigSync
================

Sync the BigIP configuration from a primary to a secondary.


**Parameters**

* ``envs`` (type: list)

  * **Required:** True

  * **Items:** Strings of environment names


**Example**

.. code-block:: yaml
   :linenos:

   hosts: ["example01.com", "example02.com"]
   steps:
       - bigip:ConfigSync:
             envs: ["qa", "stage", "prod"]
