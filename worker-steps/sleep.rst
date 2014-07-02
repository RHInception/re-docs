Sleep
*****

sleep:Seconds
=============

Pause all further playbook step execution for the given number of
seconds.

**Parameters**

* ``seconds`` (type ``int``)

  * **Required:** True
  * **Description:** The number of seconds to pause for

**Example**

To pause a playbook for 1,337 seconds:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3,4

   hosts: ['localhost']
   steps:
       - sleep:seconds:
           seconds: 1337

.. note:: If more than one host is given in ``hosts``, the playbook will pause again for each host given.
