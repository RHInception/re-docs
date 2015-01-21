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
