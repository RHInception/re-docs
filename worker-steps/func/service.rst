.. _steps_func_service:

Service
=======

The **service** module allows you to interact with system services, as
you would with the ``service`` or ``systemctl`` commands. Only one
example is included in this section because the syntax for each of the
**service** module steps are nearly identical.

Example
-------
This example demonstrates how to restart the **megafrobber** service
(see lines **3** and **4**).

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3-4

   hosts: ['localhost']
   steps:
       - service:Restart:
           service: megafrobber

To use any of the other sub-commands, on line **3** in this example we
would replace ``service:Restart`` with the desired subcommand. Such as
``service:Stop`` or ``service:Reload``.


service:Stop
------------
Stop a given service.

service:Start
-------------
Start a given service.

service:Restart
---------------
Restart a given service.

service:Status
--------------
Return the status (running, stopped, etc) of a given service.

service:Reload
--------------
Tell a service to reload it's configuration files.

.. note:: Not all system services support all the given subcommands. This is especially true for **reload**.
