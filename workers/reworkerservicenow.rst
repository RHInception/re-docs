.. _workers_servicenow:

RE-WORKER-SERVICENOW
--------------------
Release Engine Worker Plugin that does basic interaction with `Service
Now <http://www.servicenow.com/>`_.

This worker takes two configuration files. The first is the SERVICE
NOW configuration file. It should look like this:

.. code-block:: json

  {
      "servicenow_user": "username",
      "servicenow_password": "secret",
      "api_root_url": "https://127.0.0.1/api/now/v1"
  }

* Set the ``MQ config file`` parameters to sane values (see also:
  :ref:`Setting Up The Bus<setting_up_the_bus>`)
* Run the worker

  * **From source:** ``python ./replugin/servicenowworker/__init__.py -w $YOUR_SERVICE_NOW_CONF.json $YOUR_MQ_CONF.json``
  * **From install:** ``re-worker-servicenow -w $YOUR_SERVICE_NOW_CONF.json $YOUR_MQ_CONF.json``

We should see output similar to the following if everything well:

.. code-block:: bash

   [user@frober]$ re-worker-servicenow -w servicenow.json mq.json`
   2014-05-19 14:39:47,080 - ServiceNowWorker - WARNING - No app logger passed in. Defaulting to Streamandler with level INFO.
   2014-05-19 14:39:47,083 - ServiceNowWorker - INFO - Attempting connection with amqp://inceptadmin:***@messagebus.example.com:5672/
   2014-05-19 14:39:47,412 - ServiceNowWorker - INFO - Connection and channel open.
   2014-05-19 14:39:47,413 - ServiceNowWorker - INFO - Consuming on queue worker.servicenow

Creating Change Records
^^^^^^^^^^^^^^^^^^^^^^^

.. note:: Creating change records requires a non-trivial amount of
          integration work. The ``CreateChangeRecord`` subcommand will
          **not** work out-of-the-box.

Creating change records requires additional information in your worker
configuration file. Below is an example (omitting already covered
details from above):

.. code-block:: json
   :linenos:

   {
       "api_import_url": "https://127.0.0.1/api/now/v1/import/u_test_change_creation",
       "auto_create_change_if_missing": false,
       "change_record_payload":
       {
           "u_change_location": "0503586769dd3000df63506980241089",
           "u_assignment_group": "f3b9bd00d0000000ec0be80b207ce954",
           "u_end_date": null,
           "u_change_plan": "Frobnicate all the things",
           "u_backout_plan": "Frob them back again",
           "u_short_description": "Test the Megafrobber Enterprise Suite",
           "u_start_date": null
       },
       "start_date_diff": {
           "days": 7
       },
       "end_date_diff": {
           "days": 7,
           "hours": 8
       }
   }

**Description of Fields**

=================================  =====================================================================================================
Field                              Description
=================================  =====================================================================================================
``api_import_url``                 Full URL to **import set table** API endpoint
``auto_create_change_if_missing``  **Boolean** - Will create a change record automatically if a ``DoesChangeRecordExist`` step is false
``change_record_payload``          **Dictionary** - Scaffolding of new record inserted into import set table (see notes below)
``start_date_diff``                **Dictionary** - Used to calculate the future start time from now (see notes below)
``end_date_diff``                  **Dictionary** - Used to calculate the future end time from now (see notes below)
=================================  =====================================================================================================

change_record_payload
"""""""""""""""""""""

The ``change_record_payload`` dictionary is a template for new draft
change records. Any **reference field** in your instance (in this
example, ``u_change_location`` and ``u_assignment_group``) must be
defined using the ``sys_id`` of the target reference.

Leave the ``u_start_date`` and ``u_end_date`` fields as
``null``. These are filled in automatically using the
``start_date_diff`` and ``end_date_diff`` by means of:

.. code-block:: python

   u_start_date = datetime.datetime.now() + datetime.timedelta(**start_date_diff)
   u_end_date = datetime.datetime.now() + datetime.timedelta(**end_date_diff)

start/end_date_diff
"""""""""""""""""""

These dictionaries must conform to the method signature of the
`datetime.timedelta()
<https://docs.python.org/2/library/datetime.html#timedelta-objects>`_
object.



Commands
^^^^^^^^

The ServiceNow Worker steps are documented in :ref:`Worker Steps: ServiceNow <steps_servicenow>`.
