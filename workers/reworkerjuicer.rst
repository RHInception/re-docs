.. _re_worker_juicer:

RE-WORKER-JUICER
----------------

Release Engine Worker Plugin to run Juicer commands

.. admonition:: What's Juicer?

   The **Juicer** worker allows you to upload and promote batches of
   RPMs into `Yum <http://yum.baseurl.org/>`_ repositories. In juicer
   terminology, these batches of RPMs are referred to as `release
   carts
   <https://github.com/juicer/juicer/wiki/cart-json-specification>`_.


About & Usage
~~~~~~~~~~~~~

A **juicer** worker allows you to upload/promote RPMs as just another
step in your release process. **Note** however that the juicer plugin
requires that additional information is passed to it when the release
is started. See :ref:`Dynamic Variables <rerest_dynamic_variables>`
for more information on this topic.



Juicer Client Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~
See the upstream `juicer configuration
<https://github.com/juicer/juicer/blob/master/docs/markdown/config.md>`_
documentation for instructions on how to setup a system accounts
juicer configuration file.


Dependencies
~~~~~~~~~~~~
Use of the juicer worker requires a configuired and running `Pulp
Server <http://www.pulpproject.org/>`_ installation. Setup and
maintenance of pulp servers is out of scope for this
documentation. However, they provide `detailed setup instructions
<https://pulp-user-guide.readthedocs.org/en/pulp-2.3/installation.html>`_
to help get you started.
