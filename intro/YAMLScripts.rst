.. _intro_yaml:

YAML Scripts
============

This page provides a basic overview of correct YAML
syntax. Additionally it covers non-task specific modules that are
valid in `Release Engine` playbooks.

.. seealso::

   :ref:`tasks`
       The complete documentation of all included task modules

YAML Basics
-----------


For the `Release Engine`, every YAML playbook must be a list at it's root-most element. Each item in the list is a dictionary. These dictionaries
represent all the options you can use to write a `Release Engine` playbook. In
addition, all YAML files (regardless of their association with
`Release Engine` or not) all YAML documents should start with ``---``.

In YAML a list can be represented in two ways. In one way all members
of a list are lines beginning at the same indentation level starting
with a ``-`` character::

    ---
    # A list of tasty fruits
    - Apple
    - Orange
    - Strawberry
    - Mango

In the second way a list is represented as comma separated elements
surrounded by square brackets. Newlines are permitted between
elements::

    ---
    # A list of tasty fruits
    [apple, orange, banana, mango]

A dictionary is represented in a simple ``key:`` and ``value`` form::

    ---
    # An employee record
    name: John Eckersberg
    job: Developer
    skill: Elite

Like lists, dictionaries can be represented in an abbreviated form::

    ---
    # An employee record
    {name: John Eckersberg, job: Developer, skill: Elite}

.. _truthiness:

You can specify a boolean value (true/false) in several forms::

    ---
    knows_oop: True
    likes_emacs: TRUE
    uses_cvs: false

Finally, you can combine these data structures::

    ---
    # An employee record
    name: John Eckersberg
    job: Developer
    skill: Elite
    employed: True
    foods:
        - Apple
        - Orange
        - Strawberry
        - Mango
    languages:
        ruby: Elite
    python: Elite
    dotnet: Lame

That's all you really need to know about YAML to get started writing
`Release Engine` playbooks.

.. seealso::

   `YAMLLint <http://yamllint.com/>`_
       YAML Lint gets the lint out of your YAML


.. _elements_yaml:

elements of a Release Engine script
---------------------------

A Release Engine release script can specify the following keys:

* ``hosts``
* ``concurrency``
* ``output``
* ``preflight``
* ``tasks``

Each of these keys and their respective arguments is described in the
following sections.

.. include:: elements_yaml/hosts.rst
.. include:: elements_yaml/concurrency.rst
.. include:: elements_yaml/output.rst
.. include:: elements_yaml/preflight.rst
.. include:: elements_yaml/tasks.rst


Putting it all together
-----------------------

Before we finish, lets put together everything we've seen up to
now. That will include ``hosts``, ``concurrency``, ``output``,
``preflight``, and an example ``task``::

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
