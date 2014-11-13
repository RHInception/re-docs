.. _steps_func_yum:

Yum Cmd
=======

yumcmd:install
--------------
The yumcmd install command will allow you to install a package or packages to the systems defined in the playbook.

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3-4

   hosts: ['localhost']
   steps:
       - yumcmd:install:
           package: emacs

yumcmd:remove
-------------
The yumcmd remove command will remove a package installed on one system or many systems as defined by the playbook.

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3-4

   hosts: ['localhost']
   steps:
       - yumcmd:remove:
           package: emacs

yumcmd:update
-------------
Yumcmd update will update the system completely. Of note this will not currently update an individual package.

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3-4

   hosts: ['localhost']
   steps:
       - yumcmd:update
