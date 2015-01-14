.. _steps_func_yum:

Yum Cmd
=======

yumcmd:Install
--------------
The yumcmd install command will allow you to install a package or packages to the systems defined in the playbook.

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3-4

   hosts: ['localhost']
   steps:
       - yumcmd:Install:
           package: emacs

yumcmd:Remove
-------------
The yumcmd remove command will remove a package installed on one system or many systems as defined by the playbook.

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3-4

   hosts: ['localhost']
   steps:
       - yumcmd:Remove:
           package: vim

yumcmd:Update
-------------
Yumcmd update will update the system completely. Of note this will not currently update an individual package.

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3-4

   hosts: ['localhost']
   steps:
       - yumcmd:Update
