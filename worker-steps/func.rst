.. _steps_func:

FUNC
****

The FUNC worker has several commands (with their own subcommands)
available. They are all documented on this page.

.. _steps_func_puppet:

Puppet
======

The **puppet** module allows you to interact with the ``puppet``
service on remote hosts.

puppet:Run
----------

**Parameters**

The parameters to the ``Run`` subcommand can be mixed and matched
together. That is to say, *none of the parameters given below are
mutually exclusive*.


* ``noop`` (type: boolean)

  * **Required:** False
  * **Default:** ``False``
  * **Description:** Set to ``True`` to enable ``noop`` mode (show
    what *would* have happened)
  * **CLI Equivalent:** ``puppet agent --test --noop``

* ``server`` (type: string)

  * **Required:** False
  * **Default:** ``None``
  * **Description:** Specify which puppet master to compile the
    catalog against
  * **CLI Equivalent:** ``puppet agent --test --server puppetmaster01.example.com``

* ``enable`` (type: boolean)

  * **Required:** False
  * **Default:** ``False``
  * **Description:** Set to ``True`` to enable the puppet agent prior
    to running puppet. **Note** that running puppet will not be
    attempted if the **enable** command fails
  * **CLI Equivalent:** ``puppet agent --enable && puppet agent --test``

* ``tags`` (type: list of strings)

  * **Required:** False
  * **Default:** ``None``
  * **Description:** - Arguments to pass to the ``--tags`` option
  * **CLI Equivalent:** ``puppet agent --test --tags sometag anothertag moretags``


**Example**

.. code-block:: yaml
   :linenos:

   hosts: ['localhost']
   steps:
       # Basic subcommands
       - puppet:Run
       - puppet:Enable
       - puppet:Disable

       # Now with some extra options

       # Run puppet in noop mode
       - puppet:Run:
           noop: True

       # Run puppet in noop mode, and make sure the agent is enabled first
       - puppet:Enable
       - puppet:Run:
           noop: True

       # Run puppet in noop mode, and make sure the agent is enabled
       # first, but as one single step
       - puppet:Run:
           noop: True
           enable: True

       # Equivalent to 'puppet agent --test --tags yum auth package'
       - puppet:Run:
           tags:
             - yum
             - auth
             - package

puppet:Enable
-------------

**Parameters**

* The ``Enable`` subcommand does not accept any parameters

.. code-block:: yaml
   :linenos:

   hosts: ['localhost']
   steps:
       - puppet:Enable


puppet:Disable
--------------

**Parameters**

* ``motd`` (type: string or ``False``)

  * **Required:** False
  * **Default:** ``puppet disabled by Release Engine at 2014-09-16 16:27:11.075617``
  * **Description:** The ``puppet:Disable`` sub-command will
    automatically append a message to ``/etc/motd`` indicating that
    the puppet agent has been stopped. This behavior can be disabled
    by setting ``motd`` to ``False``, or customized by setting
    ``motd`` to a message of your choice. Use ``%s`` to substitute a
    datestring (per ``str(datetime.datetime.now())``) into your message

.. code-block:: yaml
   :linenos:

   hosts: ['localhost']
   steps:
       # Just disable the puppet agent, motd is still updated
       - puppet:Disable

       # Disable the agent, but don't update the motd
       - puppet:Disable
           motd: False

       # Disable the agent, and put a custom message in /etc/motd
       - puppet:Disable
           motd: "Puppet disabled for maintenance on %s"


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
