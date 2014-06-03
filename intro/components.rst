.. _components:

Components
**********

The Release Engine has three required components. Each of which is
documented thoroughly in its own separate section. The following are
brief descriptions of each component.

* :ref:`RE-CORE <re_core>`

  * A finite state machine which oversees the execution of all steps
    required to complete a release

* :ref:`RE-REST <re_rest>`

  * A `REST endpoint
    <http://en.wikipedia.org/wiki/Representational_state_transfer>`_
    which handles authentication/authorization

  * The primary point of interaction for clients

* :ref:`One or more workers <components_pre_built>`

  * Workers are the components which are actually executed as release steps
  * There are several pre-built workers, you can view them `on github <https://github.com/RHInception?query=re-worker->`_


In addition to the required components:

* :ref:`RE-CLIENT <reclient>`

  * Command line took for easily interacting with the Release Engine
  * Create, read, update, delete, and run playbooks


Component Diagram
=================

.. image:: images/ComponentDiagram.png


Interactions & Workflow
=======================

This section describes how the Release Engine components interact with
each other and the :ref:`supporting infrastructure
<setup_infra_req>`. We'll review these interaction by examining a
typical workflow.
