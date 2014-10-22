.. _components_main:

Components
==========
.. toctree::
   :maxdepth: 1
   :glob:

   components/*

.. _components_pre_built:

.. important::
   Release Engine workers require the :ref:`RE-WORKER <re_worker>` module


Playbook Workers
----------------

These workers are usable in :ref:`playbooks <playbooks>`.

.. toctree::
   :maxdepth: 1

   workers/reworkerbigip.rst
   workers/reworkerfunc.rst
   workers/reworkergit.rst
   workers/reworkersatellite5.rst
   workers/reworkerjuicer.rst
   workers/reworkerservicenow.rst
   workers/reworkersleep.rst


Auxiliary Workers
-----------------

These workers are support workers and handle various other tasks. They
are **not** usable in playbooks.

.. toctree::
   :maxdepth: 1

   workers/reworkeremailnotify.rst
   workers/reworkerircnotify.rst
   workers/reworkeroutput.rst




Libraries/Helpers
-----------------
.. toctree::
   :maxdepth: 1
   :glob:

   libraries/*
