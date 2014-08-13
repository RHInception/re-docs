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




Advanced Topics
***************

Hard stuff.


Other languages
===============

what about erlang?
