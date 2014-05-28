.. _intro_json:



JSON Scripts
************

This page provides a basic overview of correct JSON
syntax. Additionally it covers non-task specific modules that are
valid in `Release Engine` playbooks.

.. seealso::

   :ref:`Components → Pre-Built Workers <components_pre_built>`
       For more information on the workers that ship with Release Engine

For the `Release Engine`, every JSON playbook must be a list at it's root-most element.
Each item in the list is a dictionary. These dictionaries
represent all the options you can use to write a `Release Engine` playbooks.


.. tip::

    With the exception of **strings** all types (arrays, booleans, integers, numbers,
    nulls and objects) in a JSON list or dictionary are not required to be surrounded
    by double quotes (``"Foo"``). If added, these types become **strings**. Also, all
    lines in a list or dictionary must end in a comma ``,`` **except** the final member
    in the list or dictionary, which must explicitly **not** end with a comma.

In JSON a list can be represented in two ways. In one way all members
of a list are lines beginning at the same indentation level surrounded by
square brackets.

.. code-block:: json

    [
     "Apple",
     "Orange",
     "Strawberry",
     "Mango"
    ]

In the second way a list is represented as comma separated elements
surrounded by square brackets. Newlines are permitted between
elements:

.. code-block:: json

    ["apple", "orange", "strawberry", "mango"]


A dictionary is represented in a simple ``key:`` and ``value`` form:


.. code-block:: json

    {
      "skill": "Elite",
      "job": "Developer",
      "name": "John Eckersberg"
    }

Like lists, dictionaries can be represented in an abbreviated form:

.. code-block:: json

    {"skill": "Elite", "job": "Developer", "name": "John Eckersberg"}


.. _truthiness:

You can specify a boolean value (true/false) in several forms:

.. code-block:: json

    {
      "knows_oop": true,
      "likes_emacs": true,
      "uses_cvs": false
    }

Finally, you can combine these data structures:

.. code-block:: json

    {
     "name": "John Eckersberg",
     "python": "Elite",
     "job": "Developer",
     "languages": {
       "ruby": "Elite"
     },
     "foods": [
      "Apple",
      "Orange",
      "Strawberry",
      "Mango"
     ],
     "dotnet": "Lame",
     "employed": true,
     "skill": "Elite"
    }

That's all you really need to know about JSON to get started writing
`Release Engine` playbooks.

.. seealso::

   `JSONLint <http://jsonlint.com/>`_
       JSON Lint gets the lint out of your JSON

.. seealso:: Get Deeper into Playbooks

   Now that we're comfortable with JSON, let's continue on and read
   the :ref:`Playbooks <playbooks>` section for an in-depth guide of
   playbooks.
