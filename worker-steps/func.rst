.. _steps_func:

FUNC
****

The FUNC worker has several commands (with their own subcommands)
available. They are all documented on this page.

Command
=======

The **command** module allows you to run arbitrary commands on a
remote host. It has one sub-command available, **run**.

command:run
-----------

**Parameters**

* ``cmd`` (type: string)

  * **Required:** True
  * **Description:** The command to run, as it would be typed into a shell prompt

**Example**

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3-4

   hosts: ['localhost']
   steps:
       - command:run:
           cmd: puppet agent --test --color=false

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
       - service:restart:
           service: megafrobber

To use any of the other sub-commands, on line **3** in this example we
would replace ``service:restart`` with the desired subcommand. Such as
``service:stop`` or ``service:reload``.


service:stop
------------
Stop a given service.

service:start
-------------
Start a given service.

service:restart
---------------
Restart a given service.

service:status
--------------
Return the status (running, stopped, etc) of a given service.

service:reload
--------------
Tell a service to reload it's configuration files.

.. note:: Not all system services support all the given subcommands. This is especially true for **reload**.


Yum Cmd
=======

yumcmd:install
--------------
Foo

yumcmd:remove
-------------
Bar

yumcmd:update
-------------
Bob


Nagios
======

The nagios module allows you to perform common tasks in Nagios related
to downtime and notifications.


nagios:ScheduleDowntime
-----------------------

Depending on the exact invocation, ``nagios:ScheduleDowntime`` will
schedule downtime for:

* A host
* Services on a host
* A host and it's services


**Parameters**

* ``nagios_url`` (type: string)

  * **Description:** Hostname of the nagios server
  * **Required:** True
  * **Default:** None

* ``minutes`` (type: int)

  * **Description:** Number of minutes to schedule downtime for
  * **Required:** False
  * **Default:** 30

* ``service`` (type: string or list)

  * **Description:** Service, or services, to schedule downtime for
  * **Required:** False
  * **Default:** Set downtime for the host itself (services on the host will continue to alert like normal)
  * **Extras:** Use the string ``ALL`` to schedule downtime for the host as well as all services on the host. Use the string ``HOST`` to explicitly set downtime for just a host. ``HOST`` and ``ALL`` are case-insensitive.

* ``service_host`` (type: string)

  * **Description:** An alternative host to schedule downtime for
  * **Required:** False
  * **Default:** None
  * **Extras:** See example below for **service host**




**Example: Schedule Downtime for a host**

In this example we set downtime for a host. Because ``minutes`` is not
provided, the duration will be for the default of 30 minutes.

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3-5

   hosts: ['localhost']
   steps:
       - nagios:ScheduleDowntime:
           nagios_url: nagios.example.com
           service: host

As stated in the parameter documentation above, we can give the string
**host** in any mix of upper and lower case characters.



**Example: Schedule Downtime for a service**

In this example we set downtime for 15 minutes (line **5**) for a
specific service (``megafrobber``, line **6**).

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3-6

   hosts: ['localhost']
   steps:
       - nagios:ScheduleDowntime:
           nagios_url: nagios.example.com
           minutes: 15
           service: megafrobber


**Example: Schedule Downtime for several services**

Similar to the previous example, here we are setting downtime for
several services at once. Note the difference below in syntax on lines
**6** → **8** compared to line **6** above. Here we provide the
services as a list to the ``service`` parameter.

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 6-8

   hosts: ['localhost']
   steps:
       - nagios:ScheduleDowntime:
           nagios_url: nagios.example.com
           minutes: 15
           service:
              - megafrobber
              - httpd



**Example: Schedule Downtime for a host and all services on the host**

In this example we will set an hour of downtime (**60 minutes**, line
**5**) for a host and all services running on that host (line **6**).

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 5-6

   hosts: ['localhost']
   steps:
       - nagios:ScheduleDowntime:
           nagios_url: nagios.example.com
           minutes: 60
           service: ALL


**Example: Using service_host to set downtime for an alternative host**

In some deployments, **service hosts** are created in nagios to
monitor services not exactly tied to a specific host.

For example, you may be using a vendor load balancing solution, like
F5 LTM BigIPs. In a situation like this you may monitor the status of
all balancer pools so that you can send alerts if members of the pool
drop out of rotation unexpectedly.

However, while performing routine maintenance, is it expected for
hosts to be taken out of the rotation. That's what ``service_host`` is
for. Instead of setting downtime for a specific host, we might
schedule downtime for a service representing a balancer pool on our
**service host**.


.. code-block:: yaml
   :linenos:
   :emphasize-lines: 6,7

   hosts: ['localhost']
   steps:
       - nagios:ScheduleDowntime:
           nagios_url: nagios.example.com
           minutes: 60
           service_host: lb01.example.com
           service: megafrobber_pool_prod

In the above example on line **6** we tell the nagios worker that
instead of setting downtime for ``localhost``, instead, set downtime
for ``lb01.example.com``. Then on the following line (**7**) we
indicate we are setting downtime for the production `megafrobber`
balancer pool.
