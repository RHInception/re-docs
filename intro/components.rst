.. _components:

Components
++++++++++

The Release Engine has three required components:

* :ref:`RE-CORE <re_core>`

  * A finite state machine which oversees the execution of all steps
    required to complete a release

* :ref:`RE-REST <re_rest>`

  * A REST endpoint which handles authentication/authorization and
    serves as the primary point of interaction for clients

* :ref:`One or more workers <components_pre_built>`

  * Workers are the components which are actually executed as release steps
  * There are several pre-built workers, you can view them `on github <https://github.com/RHInception?query=re-worker->`_


In addition to the two required components:

* :ref:`RE-CLIENT <reclient>`

  * Command line took for easily interacting with the Release Engine
  * Create, read, update, delete, and run playbooks
