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
