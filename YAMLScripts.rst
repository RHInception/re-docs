.. _intro_yaml:



YAML Scripts
************

This page provides a basic overview of correct YAML
syntax. Additionally it covers non-task specific modules that are
valid in `Release Engine` playbooks.

.. seealso::

   :ref:`Components → Pre-Built Workers <components_pre_built>`
       For more information on the workers that ship with Release Engine

For the `Release Engine`, every YAML playbook must be a list at it's root-most element. Each item in the list is a dictionary. These dictionaries
represent all the options you can use to write a `Release Engine` playbook. In
addition, all YAML files (regardless of their association with
`Release Engine` or not) all YAML documents should start with ``---``.

In YAML a list can be represented in two ways. In one way all members
of a list are lines beginning at the same indentation level starting
with a ``-`` character

.. code-block:: yaml

    ---
    # A list of tasty fruits
    - Apple
    - Orange
    - Strawberry
    - Mango

In the second way a list is represented as comma separated elements
surrounded by square brackets. Newlines are permitted between
elements

.. code-block:: yaml

    ---
    # A list of tasty fruits
    [apple, orange, banana, mango]

A dictionary is represented in a simple ``key:`` and ``value`` form

.. code-block:: yaml

    ---
    # An employee record
    name: John Eckersberg
    job: Developer
    skill: Elite

Like lists, dictionaries can be represented in an abbreviated form

.. code-block:: yaml

    ---
    # An employee record
    {name: John Eckersberg, job: Developer, skill: Elite}

.. _truthiness:

You can specify a boolean value (true/false) in several forms

.. code-block:: yaml

    ---
    knows_oop: True
    likes_emacs: TRUE
    uses_cvs: false

Finally, you can combine these data structures

.. code-block:: yaml

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

.. seealso:: Get Deeper into Playbooks

   Now that we're comfortable with YAML, let's continue on and read
   the :ref:`Playbooks <playbooks>` section for an in-depth guide of
   playbooks.
