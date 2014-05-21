.. _playbooks:

.. caution:: **This section is a work in progress**

   **Everything you see here is subject to change without notice.**

Playbooks (Incomplete)
######################

.. contents:: Playbook Elements
   :depth: 2
   :local:

Playbooks are documents which describe the exact set of steps required
to successfully start and finish a given software release. When the
Release Engine begins :ref:`a deployment <rerest_deployment>`, the
actions it takes come directly from playbooks.

A playbook might be ran automatically each time a code builder
finishes so that it may deploy the latest snapshots. Alternatively, if
more control is desired over the release process, playbooks may be ran
by hand. Playbooks may be written in YAML syntax, or optionally in
JSON syntax.

**In this section we'll learn:**

* what a playbook looks like (by reviewing a simple example)
* the three major elements of playbooks
* the basics of how to describe steps in your playbooks, including:

 * describing a step (``name``)
 * identifying which worker to use (``plugin``)
 * passing data to the worker (``parameters``)

* advanced step description, including:

 * use of the ``dynamic`` attributes
 * use of the ``notify`` attributes

.. note:: **JSON Syntax**

   Documentation for working with JSON documents is not available yet
   at this time.

Example Playbook
*****************

.. code-block:: yaml

   ---
   - hosts:
       - foo.dev.example.com
       - bar.ops.example.com

     preflight:
       - misc.Noop

     steps:
       - bigip.OutOfRotation
       - misc.Echo:
           input: "This is a test message"


.. _elements_yaml:

Playbook Elements
*****************

A Release Engine playbook can specify the following elements:

* ``hosts``
* ``preflight``
* ``steps``

Of those elements, only ``hosts`` and ``steps`` are required. That is
to say, only the ``preflight`` element is optional.

Each of those elements and their respective usage is described in the
following sections.

.. include:: yamlscripts/hosts.rst
.. include:: yamlscripts/tasks.rst
.. include:: yamlscripts/preflight.rst

.. include:: playbook_steps.rst



Putting it all together
***********************

Before we finish, lets put together everything we've seen up to
now. That will include ``hosts``, ``preflight``, and some example
items for ``steps``.

.. code-block:: yaml

    ---
    - hosts:
        - ruby*.web.qa.example.com
        - www01.web.qa.example.com
        - www02.web.qa.example.com

    - LogOutput:
      logfile: web-restarts.log

      preflight:
        - puppet.Disable

      tasks:
        - service.Restart: {service: httpd}


On three hosts at a time this `Release Engine` script would restart the
`httpd` process, printing progress to the command line, logging it to
'web-restarts.log', and finish by emailing the result of the whole
task to my_boss@example.com.
