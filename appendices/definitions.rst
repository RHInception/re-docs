.. _intro_definitions:

Definitions
+++++++++++

AMQP
   See **Message Bus**.

Finite State Machine (**FSM**)
   See **RE-CORE**

JSON
   Javascript Object Notation. Data which can be turned into
   code. Usually returned from REST APIs. It's also called a *data
   interchange standard*.

Message Bus
   Very similar to how the postal service works, but in
   software. Clients connect to the bus and consume or publish
   messages. It's like IPC, over the network, with queues, on
   steroids.

MongoDB
   A "schemaless" document object collection.

   Basically MySQL without all the rigidly defined table structures.

Playbook
   A document describing a software release. This document is stored
   in MongoDB. Playbooks consists of three main items: ownership
   identification, target hosts, and a list of steps (See also:
   **Playbook Step**) required to finish so that the release can be
   considered completed.

Playbook Step
   A playbook step represents a unit of work in your overall release
   process.

   Defining a playbook step is like instantiating a **Worker
   Plugin**. That is to say, using the api signature of a given Worker
   Plugin, you fill in the missing parameters.

Python
   The programming language the Release Engine is primarily written in.

RE-CLIENT
   The ``re-client`` tool is how end-users primarily interact with the
   release engine. The ``re-client`` tool interfaces with the
   **RE-REST** component and provides several options for creating,
   reading, updating, and deleting playbooks.

RE-CORE
   The ring-leader of the system. Orchestrates the delegation of
   **playbook steps** to **worker plugins**. Tracks the state of a
   release in mongo and manupilates the completed/active/remaining job
   stacks as workers update the FSM.

RE-REST
   A REST endpoint (see below) which all clients attempting to
   interact with the Release Engine must proxy their commands and
   requests through. This component in integrated into the overall
   authentication/authorization scheme.

   Authorized requests made against the REST endpoint result in
   either: messages having been sent to the RE-CORE component (for
   example: begin a release for group **foo**), or in database
   create/relad/update/delete operations.

REST
   Representational State Transfer. Using the HTTP protocol in a
   programmatic way to interact with remote systems. Usually supports
   the basic CRUD operations: Creating, Reaing, Updating, and Deleting
   records.

Temporary Queue
   (See also: **Message Bus**) Temporary queues are created by various
   Release Engine components. These queues are ephemeral and usually
   automatically clean themselves up after all clients disconnect from
   them. The purpose of these temporary queues is to enable **direct
   communication** between two specific components, outside of the
   pre-defined channels of communication.

Worker (Plugin)
   Worker plugins do the actual work in a release. This could mean
   several things: running puppet on a server; restarting a host;
   uploading RPMs into a YUM repository, scheduling downtime, the
   possibilities are virtually endless.

   It might help to think of Worker Plugins as Class definitions. See
   **Playbook Step** for the other half of that comparison.

YAML
   YAML Ain't Markup Language. YAML is an alternative syntax which may
   be used to write Playbooks in. The normative syntax is JSON.

.. seealso::

    :ref:`YAML Basics<intro_yaml>`
        Introduction to YAML formatting

.. seealso::

   :ref:`Playbooks <playbooks>`
       Everything you need to know to begin writing playbooks
