.. _intro_json:



JSON Scripts
************

This page provides a basic overview of correct JSON
syntax. Additionally it covers non-task specific modules that are
valid in `Release Engine` playbooks.

.. seealso::

   :ref:`Components → Pre-Built Workers <components_pre_built>`
       For more information on the workers that ship with Release Engine

For the `Release Engine`, every JSON playbook must be a list at it's root-most element. Each item in the list is a dictionary. These dictionaries
represent all the options you can use to write a `Release Engine` playbook. In
addition, all JSON files (regardless of their association with
`Release Engine` or not) all JSON documents should start with ``---``.

In JSON a list can be represented in two ways. In one way all members
of a list are lines beginning at the same indentation level starting
with a ``-`` character

.. code-block:: json

    [
     "Apple",
     "Orange",
     "Strawberry",
     "Mango"
    ]

.. note::

   I'm not sure that we need this second way because if you parse it from yaml to json you get the same value as the first example

In the second way a list is represented as comma separated elements
surrounded by square brackets. Newlines are permitted between
elements

.. code-block:: json

    ---
    # A list of tasty fruits
    [apple, orange, banana, mango]

A dictionary is represented in a simple ``key:`` and ``value`` form

.. code-block:: json

    ---
    # An employee record
    name: John Eckersberg
    job: Developer
    skill: Elite

Like lists, dictionaries can be represented in an abbreviated form

.. code-block:: json

    ---
    # An employee record
    {name: John Eckersberg, job: Developer, skill: Elite}

.. _truthiness:

You can specify a boolean value (true/false) in several forms

.. code-block:: json

    ---
    knows_oop: True
    likes_emacs: TRUE
    uses_cvs: false

Finally, you can combine these data structures

.. code-block:: json

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

That's all you really need to know about JSON to get started writing
`Release Engine` playbooks.

.. seealso::

   `JSONLint <http://jsonlint.com/>`_
       JSON Lint gets the lint out of your JSON

.. seealso:: Get Deeper into Playbooks

   Now that we're comfortable with JSON, lets' continue on and read
   the :ref:`Playbooks <playbooks>` section for an in-depth guide of
   playbooks.
