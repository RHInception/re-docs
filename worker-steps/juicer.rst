.. _steps_juicer:

Juicer
******

juicer:promote
==============

Promote a release cart to a specified environment. It is recommended
that this command is used in an execution sequence only a single dummy
host listed in the ``hosts`` array. This will prevent the step from
being ran multiple times.


**Parameters**

* ``dynamic`` (type: list)

  * **Required:** True
  * **Items:** The strings ``environment`` and ``cart``


**Example**

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3-6

   hosts: ['localhost']
   steps:
       - juicer:promote:
           dynamic:
               - environment
               - cart


.. note::

   Recall that playbooks which have steps including :ref:`dynamic
   parameters <playbooks_steps_dynamic>` require the values for those
   parameters to be passed when starting the deployment.
