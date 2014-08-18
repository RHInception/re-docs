.. _writing_workers:

Tutorial: Writing Workers
#########################

.. contents::
   :depth: 4
   :local:

This section contains tutorials for writing your own workers. If you
haven't already, please take a few minutes and familiarize yourself
with the :ref:`re-worker API documentation <re_worker>`.


Basics
******

Simpe stuff.

Typical Worker Flow
===================

.. graphviz:: asset_sources/worker_flow/worker_flow.dot

.. note:: Not included in the chart are some of the various
          logging/notification steps which take place in a release.


Now, let's translate what this is saying into human readable words:

**initialize worker**
   In this state a process running the code representing our worker
   has just been started.

**listen to channel**
   Once the process has started our worker will open a channel to the
   message bus and begin consuming from a queue specifically dedicated
   to that kind of worker.

**receive parameters**
   Once our worker is consuming from its queue, it will sit in a
   waiting state until a message is received from the FSM
   (:ref:`re-core <re_core>`). This message is only sent when a
   release is started.

   The message will contain the parameters, or more generally
   speaking, the *configuration* of this step in the release. See the
   :ref:`re-core ↔ re-workers
   <recorereworker_deployment_message_format>` Deployment Message
   Format documentation for the specifics on what is contained in this
   information.

   **Note:** The message consumed from the message queue is a
   serialized JSON datastructure. Most workers will deserialized this
   message using a JSON library feature, such as the Python
   `json.loads()
   <https://docs.python.org/2/library/json.html#json.load>`_ method.

   The **most important** piece of information contained in this
   datastructure is the ``reply_to`` variable. This variable tells our
   worker the name of the temporary queue to continue all further
   communication with the FSM on. Messages sent to *any other*
   destination will be lost in the message exchange.

   Additionally, this first message will contain a **correlation
   ID**. This information should be recorded by our worker because it
   is used for logging and communicating back to the FSM.

**ACK parameters**
   Now that our worker has received its job parameters, the next step
   is to **acknowledge** receiving the parameters. Our worker does
   this using its AMQP librarie's ``ack`` function or method. If our
   worker is using the `Python Pika
   <https://pika.readthedocs.org/en/0.9.13/>`_ AMQP module, it will
   provide the ``delivery tag`` as the parameter to the ``ack``
   function.

**parse parameters**
   If our worker requires any unique information to do its job, then
   it must parse that information from the parameters provided by the
   FSM. This step typically involves verify everything it needs to
   operate was provided and is valid.

   This information often includes reading which sub-command (if
   applicable) to run, what (if any) hosts to operate on, parameters
   to provide to subcommands, and :ref:`dynamic values
   <messageformats_recore_dynamic>` passed in at deployment-time.

**parameters verified**
   If the parameters are parsed and it is verified that all the
   required information is present, then our worker will *reply* back
   to the FSM indicating that it is going to start running the step
   now.

   The body of the message sent back to the FSM is a JSON
   **serialized** datastructure. See the :ref:`Response Message Format
   <recore_reworker_response_format>` documentation in the re-core ↔
   re-worker docs for more information.

   Workers using the :ref:`re-worker <re_worker>` library typically
   respond by calling the :ref:`worker.send() <reworker_send>`
   method. When responding they should provide the ``reply_to``
   variable as the ``topic`` parameter and leave the ``exchange``
   parameter as an empty string.

**parameters invalid**
   Our worker must notify the FSM in the unfortunate event that the
   parameters provided were invalid. Similar to the previous step
   (valid parameters) our worker will use its ``send()`` method to
   send a :ref:`job failed <recore_reworkers_job_failed>` message.

   Once the message has been sent our worker will abort all further
   execution. If the worker is designed such that it runs in a some
   kind of io-loop (such as in the *re-worker* library), this is as
   simple as returning ``False`` while still in the ``process()``
   method.

**do the needful**
   At this point our worker has been initialized, received operating
   parameters from the FSM, and communicated back that it is going to
   proceed with the release. The next step is for the worker to begin
   doing what it was instructed to do.

   The specifics of what happens in this step are different from
   worker to worker. The :ref:`BigIP <steps_bigip>` worker, for
   example, will run one of three sub-commands at this point. The
   exact sub-command is dictated by the value of the ``subcommand``
   parameter.

**step complete**
   If *the needful* was a success, then our worker will reply back to
   the FSM one last time (again, using its ``send()`` method) with a
   JSON serialized datastructure. The message will include a
   ``status`` key set to ``completed``.

   After the message has been sent the worker will return ``True`` and
   continue its loop to begin the process all over again.

**step failed**
   If *the needful* was **not** a success, then our worker will reply
   back to the FSM one last time (again, using its ``send()`` method)
   with a JSON serialized datastructure. The message will include a
   ``status`` key set to ``failed`` and possibly another key, ``data``
   with various information about the exact nature of the failure.

   After the message has been sent the worker will return ``False``
   and continue its loop to begin the process all over again.

Exercise: Write a Worker from Scratch
=====================================

In this section we will build a worker from scratch. The worker will
be written in `Python <https://www.python.org/>`_. Additionally, the
worker will utilize the :ref:`re-worker <re_worker>` library.

To keep things simple, our new worker will pretend to `frob
<http://www.catb.org/jargon/html/F/frobnicate.html>`_ ("manipulate or
adjust, to tweak") an arbitrary *thing* and then report the
results. This worker will be called the **megafrobber** worker.

This section is separated into several sub-sections. Each sub-section
will incrementally build upon the work of the preceeding sections. At
the end, we'll have a deployable worker.

Directory Structure
-------------------

Workers adhere to the following directory structure::

   re-worker-megafrobber/     - Top level
   └── replugin/              - Python package directory
       ├── megafrobberworker/ - Worker code directory
       │   └── __init__.py    - Worker code
       └── __init__.py        - Empty file, Python module requirement


In a command-line shell, you could create this structure using the
following commands:

.. code-block:: console
   :linenos:

   $ WORKER=megafrobber
   $ mkdir -p re-worker-${WORKER}/replugin/${WORKER}worker
   $ touch re-worker-${WORKER}/replugin/__init__.py
   $ touch re-worker-${WORKER}/replugin/${WORKER}worker/__init__.py
   $ find .
   .
   ./re-worker-megafrobber
   ./re-worker-megafrobber/replugin
   ./re-worker-megafrobber/replugin/__init__.py
   ./re-worker-megafrobber/replugin/megafrobberworker
   ./re-worker-megafrobber/replugin/megafrobberworker/__init__.py


Scaffolding: Shebang and Imports
--------------------------------

.. note:: The remainder of this tutorial assumes the present working
          directory is ``re-worker-megafrobber``, the top-level
          directory

With our directory now created, we can begin filling in some
scaffolding for our new worker. All of the following code snippets go
into ``replugin/megafrobberworker/__init__.py``.

The first things we'll add are the Python `shebang
<http://www.catb.org/jargon/html/S/shebang.html>`_ and some standard
imports:

.. code-block:: python
   :linenos:

   #!/usr/bin/env python
   import reworker.worker
   import logging

The shebang (line **1**) is just there so that this script can be
executed from the command line. It tells our shell (ex: BASH) what
program to run the rest of the script in.

The import on line **2** will provide the standard **re-worker**
library for us. Finally, line **3** will allow us to properly output
application behavior.


Scaffolding: Class Definition
-----------------------------

Following our imports comes the class definition. As we noted
previously, this example worker will use the **re-worker**
library. The **re-worker** library includes one class,
``reworker.worker.Worker``.

As per the :ref:`re-worker <re_worker>` documentation, to *use* this
class, our worker must:

* Subclass ``reworker.worker.Worker`` (line **1**)
* Define a ``process`` method (line **6**)

As we can see on line **1**, we call our class ``MegafrobberWorker``.

.. code-block:: python
   :linenos:

   class MegafrobberWorker(reworker.worker.Worker):
       """
       Plugin to frob the heck out of something
       """

       def process(self, channel, basic_deliver, properties, body, output):

The parameters that we see defined on line **6** are required. This is
because of how the **re-worker** message bus integration code is
written.

1. **re-worker** connects to the bus automatically upon startup
2. **re-worker** begins consuming from the workers dedicated queue
3. Upon receiving a message a `callback
   <http://en.wikipedia.org/wiki/Callback_(computer_programming)#Python>`_
   is ran by the AMQP library (we use Pika for this). That callback
   flows into our ``process`` method
4. Once in the ``process`` method, the actual worker **work** happens
   (that's where we are now)

.. seealso::

   `The Pika Documentation <http://pika.readthedocs.org/en/latest/>`_
      You can read more about callbacks and their usage on the Pika
      website.


Scaffolding: Record Job Properties
----------------------------------

Our ``process`` method has a lot of arguments, this can appear
overwhelming. Which do we need to care about?

To get us started, here are some common setup actions we might take
with these properties.

.. code-block:: python
   :linenos:

   def process(self, channel, basic_deliver, properties, body, output):
      # Output is a logger from the python logger library. This is
      # what we report progress through
      self.output = output

      # This is the ID given to the currently happening deployment. It
      # is a unique ID used to connect all passed messages together and
      # record the deployment state in the database.
      #
      # We use it when responding to the FSM.
      self.corr_id = str(properties.correlation_id)

      # If the FSM passed us any dynamic variables, they will be in
      # the 'dynamic' key of the body parameter
      dynamic = body.get('dynamic', {})

      # reply_to is the temporary message bus queue we respond to the
      # FSM through
      self.reply_to = properties.reply_to


Scaffolding: Make It Runnable
-----------------------------

There are only two more things we need to add to make our worker
runnable from the command line. The first is a ``main`` function, the
second is the code to call that function when requested. These should
go at the **end** of the file.


.. code-block:: python
   :linenos:

   def main():  # pragma: no cover
       from reworker.worker import runner
       runner(MegafrobberWorker)


   if __name__ == '__main__':  # pragma: no cover
       main()


Note on line **3** that we pass the name of our class to the
``runner`` function.




Advanced Topics
***************

Hard stuff.


Message Queue Bindings
======================

This section will describe how to configure your message queue
bindings so that messages are delivered to the right workers.

..
   * a binding from worker.juicer must exist, routing to whatever queue the worker is listening on (presumably, worker.juicer)
   * running worker instances can pick whatever queue they want to listen on. this has important implications:
   * You could have two juicer workers running. One listening on worker.juicer, one on worker.secretjuice
   * If you did this, you could have two different step definitions in your playbook:
     * juicer:promote
     * secretjuice:promote
   * Each would route to a different juicer worker, respectively.



Other languages
===============

what about erlang, go, hack, or node.js?
