.. _appendicies_logging:

Logging
*******

The :ref:`re-core <re_core>` and :ref:`re-rest <re_rest>` components
have robust logging facilities. Features and configuration options
include:

* useful log-level thresholds
* log file location setting
* per-deploy context accumulation
* separate per-deploy log files (for :ref:`re-core
  <re_core_pre_release_logging>` only)
* human-readable, machine-parsable log record format

.. _appendicies_logging_thresholds:

Thresholds
==========

Configuring the log level thresholds allows you to see less or more of
what's going on behind the scenes. We stick to the philosophy that an
admin should be able to understand what's happening without requiring
DEBUG level logging.

**re-core** will only print ``WARNING`` or higher level records on the
console at run-time. This includes ``ERROR`` and ``CRITICAL`` messages
as well. For example::

   2014-12-10 19:45:34.659127 +0000 - app_component="recore" - source_ip="" - log_level="INFO" - playbook_id="" - deployment_id="" - user_id="" - active_step="" - deploy_phase="" - message="Initialized core logging with level=INFO and log file=/tmp/recore.log"
   2014-12-10 19:46:29.880620 +0000 - app_component="recore" - source_ip="" - log_level="CRITICAL" - playbook_id="" - deployment_id="" - user_id="" - active_step="" - deploy_phase="" - message="SIGINT received - killing all threads and then finishing the main process"


**re-rest**, if running from source, will only print out basic request
strings on the console::

   127.0.0.1 - - [10/Dec/2014 14:46:00] "PUT /api/v0/test/playbook/542423e102b67c000f941dcb/deployment/ HTTP/1.1" 201 -

The remainder of the re-rest log records will go into the specified
log file.


.. _appendicies_logging_context_accumulation:

Context Accumulation
====================

To enable transactional searching re-rest and re-core *accumulate* log
field data over the course of each deployment. This means that as more
information about a release is discovered, it is saved in memory and
used to fill in the matching log record field.

For example, **re-rest** will know the clients source IP address
almost immediately. This is saved in memory for the duration of the
deployment. If the client is running a playbook deployment the
playbook ID will be stored in memory as well. Once re-rest receives a
confirmation message from re-core then the unique deployment ID will
be recorded and saved as well.

Here are some snippets (with a few fields removed for formatting
purposes) from a re-rest deployment log demonstrating how context
information accumulates and is later incorporated.

*User is authenticated at this point, re-rest must connect to the
message bus and ask the FSM to start a deployment*::

   source_ip="127.0.0.1" - playbook_id="abcdefg123456" - deployment_id="" - user_id="tbielawa" - message="Creating a job for group test/abcdefg123456"

*Back in re-core a log record such as this would be emitted indicating re-core received a request from re-rest*::

   source_ip="127.0.0.1" - log_level="INFO" - playbook_id="abcdefg123456" - deployment_id="" - user_id="tbielawa" - active_step="" - deploy_phase="" - message="New job requested, starting release process for test ..."

*re-core will create a new deployment and accumulate that ID in it's own context accumulator*::

   source_ip="127.0.0.1" - log_level="INFO" - playbook_id="abcdefg123456" - deployment_id="098765abcdefg" - user_id="tbielawa" - active_step="" - deploy_phase="" - message="Emitted message to start new release for abcdefg123456. Job id: 098765abcdefg"

*Back in re-rest, note how* ``deployment_id`` *is not filled in yet*::

   source_ip="127.0.0.1" - playbook_id="abcdefg123456" - deployment_id="" - user_id="tbielawa" - message="Parsing bus response for request id ff26db4cdafe"

*re-rest has parsed the response and added the deployment ID to its context accumulator*::

   source_ip="127.0.0.1" - playbook_id="abcdefg123456" - deployment_id="098765abcdefg" - user_id="tbielawa" - message="Got job id of 098765abcdefg for request id ff26db4cdafe"

Tools such as Splunk or Logstash allow searching based on
transactions. A transaction is a collection of related log events
(such all the records for a deployment). These transactions rely on
log record events having one or more fields with the same value. By
collecting deployment context in the way shown above users are enabled
to perform distributed transactional searching over their log archives
by specifying fields like ``deployment_id`` and ``playbook_id`` in
their transaction searching operators.


.. _appendicies_logging_parsing:

Log Parsing
===========

As we saw in the previous section, these log records conform to a very
specific format: ``DATE_STRING - field1="value" - field2="value" -
field3="value" ...``. This turns both reading and parsing the logs
into simple tasks.

Here's an example in Python of how we might parse by field-name one of
the log records shown above.

.. code-block:: python
   :linenos:

   In [1]: import re

   In [2]: pattern = '^(?P<year>\d{4})-(?P<month>\d{2})-(?P<day>\d{2}) (?P<hour>\d{2}):(?P<minute>\d{2}):(?P<second>\d{2})\.(?P<ms>\d+) ?(?P<tzoffset>([\+\-]\d{4})*) \- app_component="(?P<appname>[^\"]*)" \- source_ip="(?P<source_ip>(\d{,3}\.\d{,3}\.\d{,3}\.\d{,3})*)" \- log_level="(?P<level>[A-Z]*)" - playbook_id="(?P<playbookid>[^\"]*)" \- deployment_id="(?P<deploymentid>[^\"]*)" \- user_id="(?P<user>[^\"]*)" \- message="(?P<msg>[^\"]*)"$'

   In [3]: log_str = '2014-10-13 16:29:45.123654 +0000 - app_component="recore" - source_ip="123.123.123.123" - log_level="INFO" - playbook_id="1234567890" - deployment_id="0987654321" - user_id="someone" - message="Deployment started"'

   In [4]: re.match(pattern, log_str).groupdict()
   {'appname': 'recore',
    'day': '13',
    'deploymentid': '0987654321',
    'hour': '16',
    'level': 'INFO',
    'minute': '29',
    'month': '10',
    'ms': '123654',
    'msg': 'Deployment started',
    'playbookid': '1234567890',
    'second': '45',
    'source_ip': '123.123.123.123',
    'tzoffset': '+0000',
    'user': 'someone',
    'year': '2014'
   }
