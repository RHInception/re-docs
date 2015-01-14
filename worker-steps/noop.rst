.. _steps_noop:

noop
****

noop:Fail
=========

Force a deployment to fail.


noop:AnythingElse
=================

Any other subcommand given to the ``noop`` worker will automatically
succeed.


**Example**

The first step will pass, the second step will fail.

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3,4

   hosts: ['localhost']
   steps:
       - noop:MegaFrobber
       - noop:Fail
