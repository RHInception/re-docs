.. _steps_satellite5:

Satellite5
**********

satellite5:Promote
==================

Promote all RPM's from one channel into another channel. Under the
covers this utilizes the ``channel.software.mergePackages`` API call.

**Dynamic Parameters**

* ``promote_from_label`` (type ``str``)

  * **Required:** True
  * **Description:** Channel to promote packages **from**


* ``promote_to_label`` (type ``str``)

  * **Required:** True
  * **Description:** Channel to promote packages **to**


**Example**

Promote packages from **sourcechannel** into **destinationchannel**

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3-5

   hosts: ['localhost']
   steps:
       - satellite5:Promote:
           promote_from_label: sourcechannel
           promote_to_label: destinationchannel
