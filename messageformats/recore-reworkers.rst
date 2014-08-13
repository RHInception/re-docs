.. _recore_reworkers:

re-core ↔ re-workers
----------------------

While executing releases, the **re-core** component emits messages in
one of three formats. The format selected is determined by the type of
:ref:`execution step <yaml_steps>` being ran.

:ref:`Simple <messageformats_recore_simple>`
   For execution steps which :ref:`require no arguments
   <playbooks_steps_strings>`

:ref:`Argument <messageformats_recore_argument>`
   For execution steps which require :ref:`defined
   <playbooks_steps_arguments>`

:ref:`Dynamic <messageformats_recore_dynamic>`
   For execution steps which require :ref:`dynamic
   <playbooks_steps_dynamic>` arguments

In the rest of this section we will find that these three formats are
very similar in structure. Each section will highlight the elements
which make the respective format unique from the others.


.. _recorereworker_deployment_message_format:

Deployment Message Formats
~~~~~~~~~~~~~~~~~~~~~~~~~~

This section describes the message formats emitted by **re-core** to
workers.


.. _messageformats_recore_simple:

Simple Message Format
`````````````````````

On line **8** in the following playbook we can see there is only one
step to execute, ``bigip:OutOfRotation``. From how the step is
written, as a simple string, we know that this step requires no
arguments. That is to say, we do not need to define any parameters in
the playbook or provide any dynamic data when starting the release.

.. code-block:: json
   :linenos:
   :emphasize-lines: 8

   {
       "name": "docs test",
       "group": "test",
       "execution": [
           {
               "description": "sequence 0",
               "hosts": [ "host01.example.com" ],
               "steps": [ "bigip:OutOfRotation" ]
           }
       ]
   }

For an execution step like this (line **8**), **re-core** selects the
simple message format. The following example shows the format of the
message which it would emit to the bus.

.. code-block:: json
   :linenos:
   :emphasize-lines: 2,3,4,5

   {
       "parameters": {
           "hosts": [ "host01.example.com" ],
           "command": "bigip",
           "subcommand": "OutOfRotation"
       },
       "group": "test",
       "dynamic": {},
       "notify": {}
   }

This is the simplest message format used by **re-core** when
interacting with simple workers. As such, we can see that it's quite
terse. The `Argument` and `Dynamic` message formats use this same
structure, however they fill in different items.

On line **2** we have the ``parameters`` item. This holds the basic
information required for this worker to complete its job:

``hosts``
   An array of hosts to apply the step to

``command``
   The name of the worker being utilized

``subcommand``
   The specific action the worker should take

Additionally the ``parameters`` item has two sibblings: ``group`` and
``dynamic``. These items are always sent to the worker, even if (as in
this example), there is no dynamic data to send.


.. _messageformats_recore_argument:

Argument Message Format
```````````````````````

On line **10** in the following playbook we can see there is only one
step to execute, ``service:Restart``. From how the step is written, as
a dictionary, we know that this step requires one argument,
``service`` which is defined as ``megafrobber`` (line **11**).

.. _recorereworkers_playbook:

.. code-block:: json
   :linenos:
   :emphasize-lines: 10,11

   {
       "name": "docs test",
       "group": "test",
       "execution": [
           {
               "description": "sequence 0",
               "hosts": [ "host01.example.com" ],
               "steps": [
                   {
                       "service:Restart": {
                           "service": "megafrobber"
                       }
                   }
               ]
           }
       ]
   }

For an execution step like this (line **10**), **re-core** selects the
`argument` message format. The following example shows the format of the
message which it would emit to the bus.

.. code-block:: json
   :linenos:

   {
       "parameters": {
           "service": "megafrobber",
           "hosts": [
               "host01.example.com"
           ],
           "subcommand": "Restart",
           "command": "service"
       },
       "group": "test",
       "dynamic": {},
       "notify": {}
   }


What makes this message format different from the previous format is
the presence of an additional key in the ``parameters`` item. That key
is ``service`` (line **3**). This comes directly from line **11** in
the example playbook.


.. _messageformats_recore_dynamic:

Dynamic Message Format
``````````````````````

Still referencing the previous playbook (:ref:`Argument Message Format
<messageformats_recore_argument>`), let's add an execution step which
requires dynamic arguments (this example only shows the additional
step).

.. code-block:: json
   :linenos:
   :emphasize-lines: 2,3,4,5

   {
       "juicer:Promote": {
           "dynamic": [
               "cart",
               "environment"
           ]
       }
   }


.. seealso::

   :ref:`RE-WORKER-JUICER <re_worker_juicer>`
      Documentation for the **re-worker-juicer** worker

On line **2** we see that the execution step is called
``juicer:Promote``. On the following line we see the dictionary key
``dynamic``, and that it's value is a ``list`` type. The items in the
list (lines **4** → **5**) indicate the required **dynamic** variables
to run the step. This step requires two such variables, ``cart`` and
``environment``. The user would supply the values for these variables
when starting the release.

.. note::

   For more information on dynamic variables see :ref:`Dynamic
   Variables <rerest_dynamic_variables>`.

The following example shows the format of the message which
**re-core** would emit to the bus.

.. code-block:: json
   :linenos:
   :emphasize-lines: 14,15,16

   {
       "parameters": {
           "command": "juicer",
           "dynamic": [
               "cart",
               "environment"
           ],
           "subcommand": "Promote",
           "hosts": [
               "host01.example.com"
           ]
       },
       "group": "test",
       "dynamic": {
           "cart": "bitmath",
           "environment": "re"
       },
       "notify": {}
   }

Here we see a familiar key appearing in the ``parameters`` item,
``dynamic``.

.. warning::

   In future releases, the ``dynamic`` key will **not** be copied to
   workers in the ``parameters`` item. It will only appear as a
   sibbling of the ``parameters`` item.

Now, different from the previous format (`argument`), we see the
``dynamic`` item (sibbling to ``parameters``) contains actual
key-values (lines **14** → **16**).

``dynamic``
   A dictionary with the required dynamic variables for a worker to
   run. The type of each argument is dictated by the respective
   worker.





Per-Step Notifications
``````````````````````

Auxiliary to the formats we've just discussed, are per-step
:ref:`notifications <notify>`. `Per-step notifications` are an
**optional** item which may be added to any given step (`simple`,
`argument`, and `dynamic`). Using the :ref:`previous example playbook
<recorereworkers_playbook>` for reference, we would see notifications
defined as in lines **6** → **8**, below:


.. code-block:: json
   :linenos:
   :emphasize-lines: 6,7,8

   {
       "steps": [
           {
               "service:Restart": {
                   "service": "megafrobber",
                   "notify": {
                       "started": {
                           "irc": [ "PHB", "#teamchannel" ]
                       }
                   }
               }
           }
       ]
   }

The corresponding message sent to workers, with the additional
``notify`` item would look like lines **12** → **14** in the following
example.

.. code-block:: json
   :linenos:
   :emphasize-lines: 12,13,14

   {
       "parameters": {
           "service": "megafrobber",
           "hosts": [
               "host01.example.com"
           ],
           "subcommand": "Restart",
           "command": "service"
       },
       "group": "test",
       "dynamic": {},
       "notify": {
           "started": {
               "irc": [ "PHB", "#teamchannel" ]
           }
       }
   }

.. seealso::

   :ref:`Playbooks - Notify <notify>`
      The documentation for `notify` elements in playbooks. That
      section defines the allowed syntax for the `notify` item.


Response Message Formats
~~~~~~~~~~~~~~~~~~~~~~~~

Complimenting the :ref:`Deployment Message Formats
<recorereworker_deployment_message_format>` are the `Response Message
Formats`. There are three status messages which workers may reply to
**re-core** with. This section describes the messages which
:ref:`workers <re_worker>` send to the :ref:`re-core <re_core>`
component.

General Syntax
``````````````

Status messages are defined as:

* **Type:** ``dict``
* **Required Keys:** `status`

  * **Type:** ``string``
  * **Allowed Values:**

    * **started**
    * **completed**
    * **failed**

* **Optional Keys:** `data`

    * **Type:** Any `JSON Serializable datastructure <https://docs.python.org/2/library/json.html#json.dump>`_


Job Started
```````````

After a worker has received a message from **re-core**, the message
payload is inspected for correctness. If the message payload is
successfully verified then the worker will reply to **re-core** with a
status update message indicating the job has been started:

.. code-block:: json

   {
       "status": "started"
   }


Job Completed
`````````````

Once a worker has completed the job it was given(without errors), the
worker will reply to **re-core** with a status update message
indicating success:

.. code-block:: json

   {
       "status": "completed"
   }

**Optionally** a worker may reply to **re-core** with an additional
item, ``data``. The value of the ``data`` key may be of any type.

**Example**

.. code-block:: json
   :linenos:
   :emphasize-lines: 3,4,5

   {
       "status": "completed",
       "data": {
           "items_frobbed": 1337,
           "avg_time_to_frob_ms": 100
       }
   }

On line **3** we see the ``data`` key being defined in the response
message. On lines **4** and **5** we see two additional items being
reported: ``items_frobbed`` (the number of items which were
`frobnicated <http://www.catb.org/jargon/html/F/frobnicate.html>`_)
and ``avg_time_to_frob_ms``, the average amount of time (in
miliseconds) it took to `frob` each item.

.. important::
    The ``data`` item is not currently used by any Release Engine
    component

    Remember that in the previous example, ``items_frobbed`` and
    ``avg_time_to_frob_ms`` are just made-up examples. In reality,
    workers should use the :ref:`notification system
    <notification_message_format>` for communicating such information.


Job Failed
``````````

If for some reason a worker cannot start a job (for example, due to
insufficient or incorrect parameters), or if there is an error while
executing the job, then the worker will reply to **re-core** with a
status update message:

.. code-block:: json

   {
       "status": "failed"
   }
