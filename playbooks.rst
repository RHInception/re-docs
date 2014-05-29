.. _playbooks:

Playbooks
#########

.. contents::
   :depth: 4
   :local:

Playbooks are documents which describe the exact set of steps required
to successfully start and finish a given software release. When the
Release Engine begins :ref:`a deployment <rerest_deployment>`, the
actions it takes come directly from playbooks.

A playbook might be ran automatically each time a code builder
finishes so that it may deploy the latest snapshots. Alternatively, if
more control is desired over the release process, playbooks may be ran
by hand. Playbooks may be written in YAML syntax, or optionally in
:ref:`JSON Syntax <intro_json>`.

**In this section we'll learn:**

* what a playbook looks like (by reviewing a simple example)
* the major items of playbooks
* the basics of how to describe ``execution`` steps in your playbooks, including:

 * describing a release step
 * identifying which worker to use
 * passing data to the worker

Example Playbook
****************

Here is an example of what a **super simple** playbook looks like. The
playbook is `owned` by the group called **inception**.

When ran, all this would do is restart the ``httpd`` service on
``foo.bar.example.com``

.. _simple_playbook:

.. code-block:: yaml
   :linenos:

   ---
   group: inception
   name: Simple playbook
   execution:
     - description: restart httpd
       hosts:
         - foo.bar.example.com
       steps:
         - service.Restart: {service: httpd}

.. _elements_yaml:

Playbook Components
*******************

A Release Engine playbook is made up of the following **required**
items:

group
    A short string (acronyms are best) defining the ownership of this
    playbook. Think of it like the unix group a team might all be
    members of.

    The ``group`` in our example is **inception**

execution
    A list of playbook `execution sequences`. These execution
    sequences are composed of release steps and are accompanied by
    supporting meta-data. These sequences are explained fully in
    :ref:`Execution Sequences <playbooks_exec_seq>`.

    In our example, we have one execution sequence with one release
    step (``service.Restart``).

Additionally, a playbook may define the optional item:

name
    A short description of what this playbook accomplishes overall.

    In our example the name is **Simple playbook**.



.. _playbooks_exec_seq:

Execution Sequences
*******************

Recall that ``execution`` items hold a ``list`` type. Each item in the
list is an `execution sequence`. This section describes exactly what
execution sequences do, and how to write our own.

In our example, :ref:`simple playbook <simple_playbook>`, the
execution sequence is defined in lines **5 → 9**. Let's review those
lines again:


.. code-block:: yaml
   :linenos:
   :emphasize-lines: 5,6,7,8,9

   ---
   group: inception
   name: Simple playbook
   execution:
     - description: restart httpd
       hosts:
         - foo.bar.example.com
       steps:
         - service.Restart: {service: httpd}

Like playbooks themselves, each execution sequence is comprised of
several required and optional elements. In `Sample playbook` we can
see several items are already being used: ``description``, ``hosts``,
and ``steps``. The following sections will describe these, and all
other items which are allowed in execution steps.

Required Items
++++++++++++++

This section describes the **required** items in execution sequences.

.. include:: yamlscripts/hosts.rst
.. include:: yamlscripts/tasks.rst

Optional Items
++++++++++++++

This section describes the **optional** items which are allowed in
execution sequences.

.. include:: yamlscripts/description.rst
.. include:: yamlscripts/preflight.rst



Putting it all together
***********************

To finish up, let's put together everything we've seen up to now. That
will include ``hosts``, ``preflight``, and some example items for
``steps``.

.. literalinclude:: reference_playbook.yaml
   :language: yaml
   :linenos:


.. todo:: Describe interesting parts of the previous example
