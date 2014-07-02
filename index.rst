Release Engine
==============

Release Engine is a collection of open-source tools for managing
automated software releases. The home of the Release Engines is on
GitHub in the `RHInception Organization
<https://github.com/RHInception?query=re->`_.

Put very simply: the Release Engine is an orchestration tool
(:ref:`re-core <re_core>`) that runs commands on purpose-built workers
(:ref:`re-worker <re_worker>`). The commands to run (and where to run
them) are defined in :ref:`playbooks <playbooks>` in YAML or JSON
format.

Interaction with the engine happens via a REST interface
(:ref:`re-rest <re_rest>`). Additional workers exist for the purposes
of aggregating logs, as well as sending notifications over any
preferred method (such as email, or IRC). A bare-minimal Release
Engine installation would require **re-rest**, **re-core**, and any
given **re-worker**.

To learn more about the RH Inception group, follow us on the `Red Hat
Developer Blog <http://developerblog.redhat.com/>`_ under the tag
`inception <http://developerblog.redhat.com/tag/inception/>`_.

.. toctree::
   :maxdepth: 2
   :numbered:

   intro.rst
   Setting up <setup.rst>
   Components and Libraries <components.rst>
   development.rst
   messageformats.rst
   playbooks.rst
   worker-steps.rst
   appendices.rst
   license.rst
