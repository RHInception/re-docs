.. _yaml_steps:

Steps
^^^^^

* **Required**: Yes
* **Argument** type: ``list``
* **Default**: None

The ``steps`` element defines the steps that will be performed on each
host in ``hosts``. The syntax of each possible step varries, however
they will assume one of three legal forms, respective to the
information (if any) which the execution step requires to run. In
brief, these forms are described below:

* Some steps require no information at all, as such they are given as strings
* Some require explicit parameters given in the form of keyword arguments
* Similar to the previous form, some steps require enumerating their
  `dynamic` parameters

.. todo:: Link to the `dynamic` section

Steps - Strings
"""""""""""""""

An execution step may be a simple string. To us, this means that this
given step requires no additional information. The actual `workers`
implementing each step may include possible several
sub-commands. Because of this it is common to see step names given in
a `dotted.notation`. In this form the string leading up to but not
including the full-stop (".") character refers to the worker or
module. The string after the full-stop character refers to the
specific sub-command to run.

OK. Enough of the boring stuff. Let's see some examples.

**Assume** there is a module called ``logrotate``, this module provides
one sub-command:

* ``rotate``

The documentation for the ``logrotate`` module tell us that the
``rotate`` sub-command requires no keyword parameters. That is to say,
we can define it in our execution steps as a simple string. Recall
that steps are denoted using dotted notation where the module name
comes first, followed by the sub-command. In this case our module is
``service`` and our sub-command is ``rotate``. We can see this on line
**9** in the following example:


.. code-block:: yaml
   :linenos:
   :emphasize-lines: 9

   ---
   group: inception
   name: Simple playbook
   execution:
     - description: Rotate all the megafrobber logs
       hosts:
         - foo.bar.example.com
       steps:
         - logrotate.rotate

But what is happening exactly in this example? On line **9** we see
the entry: ``- logrotate.rotate``. Recall that each step is given as
an item to the ``steps`` list. This is why line **9** in the previous
code example begins with a hyphen character. In YAML this indicates a
list item.

**Note** closely exactly how we gave ``logrotate.rotate``, because in
the next example this will change very slightly.

Steps - Keyword Arguments
"""""""""""""""""""""""""

Now let us assume there is a module called ``service`` which can
control system services. The documentation for this module tells us
that there are three sub-commands provided: ``start``, ``stop``, and
``restart``. Additionally, the documentation tells us that each
sub-command requires one keyword parameter: ``service``. On lines
**9** and **10** in the following example, we see how to provide
keyword arguments to steps:


.. code-block:: yaml
   :linenos:
   :emphasize-lines: 9,10

   ---
   group: inception
   name: Simple playbook
   execution:
     - description: Restart the megafrobber service
       hosts:
         - foo.bar.example.com
       steps:
         - service.restart:
             service: megafrobber

Or in YAML shorthand (only focusing on the step definition)

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3

   ---
   # ...
         - service.restart: {service: megafrobber}


Let's look closer at this and see exactly what is happening.

**Recall** that playbooks are **YAML Documents**. As such, there are
defined ways to describe different datastructures. Review the
`dictionary` section in :ref:`intro_yaml` if you need a refresher.

The ``service.restart`` sub-command requires one parameter,
``service``. You describe parameters in execution steps by using a
hash, or dictionary. For our example, a dictionary describing a
keyword ``service`` with value ``megafrobber`` would look like the
following example in YAML:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 2

   ---
   service: megafrobber

Additionally, recall that you can nest datastructure in YAML. If we
wanted to represent a list of dictionaries, we could do that in the
following way:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 2

   ---
   - thingies:
       service: megafrobber

Or in alternative representation:

.. code-block:: json
   :linenos:
   :emphasize-lines: 2

   ---
   [{thingies: {service: megafrobber}}]


Now that we know all of this, to give the required parameters to our
step we will define the step as a **dictionary key** with a
nested-dictionary describing our parameters. This is shown on lines
**8** and **9** in the following example:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 8,9

   ---
   # ...
   execution:
     - description: Restart the megafrobber service
       hosts:
         - foo.bar.example.com
       steps:
         - service.restart:
             service: megafrobber

.. important::
   Note the syntax change
      In the previous example we only gave the string:
      ``logrotate.rotate``. Now, instead of a string we're describing a
      **dictionary key**.

Therefore, the text for this step begins with a hyphen character (to
indicate a list item) and ends with a colon character.

Finally, on line **4** you see the provided parameters.

If there were a module which required more than one parameter, the
syntax is very similar. Lines 4 → 6 show this in the following
example:


.. code-block:: yaml
   :linenos:
   :emphasize-lines: 4,5,6

   ---
   # ...
         - service.restart:
             service: megafrobber
             foo: bar
             noop: true



.. All of the tasks are documented in the :ref:`steps` section.
