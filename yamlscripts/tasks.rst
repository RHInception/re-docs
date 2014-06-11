.. _yaml_steps:

Steps
^^^^^

* **Required**: Yes
* **Argument** type: ``list``
* **Default**: None

The ``steps`` element defines the steps that will be performed on each
host in ``hosts``. The syntax of each possible step varies, however
they will assume one of three legal forms, respective to the
information (if any) which the execution step requires to run. In
brief, these forms are described below:

* Some steps require no information at all, as such they are given as strings
* Some require explicit parameters given in the form of keyword arguments
* Similar to the previous form, some steps require an enumeration of
  their :ref:`dynamic <playbooks_steps_dynamic>` parameters


.. _playbooks_steps_strings:

Steps - Strings
"""""""""""""""

An execution step may be a simple string. To us, this means that this
given step requires no additional information. The actual `workers`
implementing each step may include possible several
sub-commands. Because of this it is common to see step names given in
colon delimited notation. In this form the string leading up to but
not including the colon (":") character refers to the worker or
module. The string after the colon character refers to the
specific sub-command to run.

OK. Enough of the boring stuff. Let's see some examples.

**Assume** there is a module called ``logrotate``, this module provides
one sub-command: ``Rotate``.

The documentation for the ``logrotate`` module tell us that the
``Rotate`` sub-command requires no keyword parameters. That is to say,
we can define it in our execution steps as a simple string. Recall
that steps are denoted using dotted notation where the module name
comes first, followed by the sub-command. In this case our module is
``logrotate`` and our sub-command is ``Rotate``. We can see this on line
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
         - logrotate:Rotate

But what is happening exactly in this example? On line **9** we see
the entry: ``- logrotate:Rotate``. Recall that each step is given as
an item to the ``steps`` list. This is why line **9** in the previous
code example begins with a hyphen character. In YAML this indicates a
list item.

**Note** closely exactly how we gave ``logrotate:Rotate``, because in
the next example this will change very slightly.

.. _playbooks_steps_arguments:

Steps - Keyword Arguments
"""""""""""""""""""""""""

Now let us assume there is a module called ``service`` which can
control system services. The documentation for this module tells us
that there are three sub-commands provided: ``Start``, ``Stop``, and
``Restart``. Additionally, the documentation tells us that each
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
         - service:Restart:
             service: megafrobber

Or in YAML shorthand (only focusing on the step definition)

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3

   ---
   # ...
         - service:Restart: {service: megafrobber}


Let's look closer at this and see exactly what is happening.

**Recall** that playbooks are **YAML Documents**. As such, there are
defined ways to describe different datastructures. Review the
`dictionary` section in :ref:`intro_yaml` if you need a refresher.

The ``service:Restart`` sub-command requires one parameter,
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
following way. Here's an example of a list of nested dictionaries:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 2,3,4,5

   ---
   - thingies:
       service: megafrobber
   - stuffs:
       penguins: cute

Or in alternative representation:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 2

   ---
   [{thingies: {service: megafrobber}}, {stuffs: {penguins: cute}}]


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
         - service:Restart:
             service: megafrobber

.. important::
   Note the syntax change
      In the previous example we only gave the string:
      ``logrotate:Rotate``. Now, instead of a string we're describing a
      **dictionary key**.

Therefore, the text for this step begins with a hyphen character (to
indicate a list item) and ends with a colon character.

Finally, on line **4** you see the provided parameters.

If there were a module which required more than one parameter, the
syntax is very similar. Lines **4** → **6** show this in the following
example:


.. code-block:: yaml
   :linenos:
   :emphasize-lines: 4,5,6

   ---
   # ...
         - service:Restart:
             service: megafrobber
             foo: bar
             noop: true

.. _playbooks_steps_dynamic:

Steps - Dynamic Arguments
"""""""""""""""""""""""""

This section is about `dynamic arguments`. Dynamic arguments differ
from normal arguments in that their values are not stored in
playbooks. Rather, within a playbook, we assert that their values will
be provided by the calling client when starting a deployment. The
syntax for defining dynamic arguments differs only slightly from how
keyword arguments are defined.

The scope of this section is limited to the role of dynamic arguments
in **playbooks** only. That is to say, discussion of dynamic arguments
in `worker development` will not be covered here. Instead, see the
:ref:`re-worker documentation <re_worker_implementation>` for that
information.

.. caution::
   The ability for clients to provide a broad spectrum of dynamic data is both a pro and a con.

      If you're writing a new :ref:`worker <re_worker>`, think very
      carefully before making arguments dynamic. Consider if the there
      is a `non-interactive` way for the information to be obtained
      instead.

Use Cases
|||||||||

Situations where dynamic arguments may be required are generally
limited to actions which require data that changes every, or nearly
every, release. Examples might include:

* `Change Record IDs <http://en.wikipedia.org/wiki/Change_management>`_
* `User Story IDs <http://www.scrumalliance.org/community/articles/2010/april/new-to-user-stories>`_

  * `or any other agile/scrum related work item`

* A target `environment <http://en.wikipedia.org/wiki/Development_environment_(software_development_process)>`_

For a more complete example, imagine our workplace has strict policies
around software releases. These policies state that any software
release must have an associated change record with it. Additionally
the policy states that every time a release happens for a change, an
update to the change record document must be recorded. This update
must indicate the date of the release.

In this situation, the pragmatic approach to automating this task
would be to develop a worker which can interface with the change
management system and add updates to the change record over an
API. Let's pretend such a worker already exists.

It is clear that we cannot hard-code change record numbers into the
worker. And storing this information in the playbook would require a
manual update to the playbook every time a new change is
created. Furthermore, this limitation effectively nullifies the
ability of a playbook to be used for two changes happening in an
overlapping time span.

This is an excellent opportunity to use dynamic variables.

----

The following examples will be using a fictional worker called
*change*. This worker has one usable function: ``Update``. The
**change** worker documentation provides the following API signature
for the ``change.Update`` function:

* **Function:** ``change.Update``

   * **Arguments:**

      * None

   * **Dynamic Arguments:**

      * **Name:** id

         * **Required:** ``True``
         * **Type:** ``string`` or ``int``
         * **Description:** The ID of the change record to update

For the rest of this section, let's pretend our ``id`` is **CHG1337**.


Dynamic Argument Syntax
|||||||||||||||||||||||

Let's begin by considering our example :ref:`simple playbook
<simple_playbook>` again.

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
         - service:Restart: {service: httpd}

However, instead of just restarting the `httpd` service, we have to
have an additional step: updating the change record (``CHG1337``). In
this section we will learn how add that step.

The general syntax for defining a step with dynamic arguments is shown
on lines **4** → **9** in the following example:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 4,5,6,7,8,9

   ---
   # ...
       steps:
         - worker:Function:
            dynamic:
               - arg_name_0
               - arg_name_1
               - arg_name_2

**Line 4**
   As before, we begin by providing the **worker.Function** name,
   ending with a ``:`` character

**Line 5**
   We define a dictionary key called ``dynamic``. **Again** note, this
   must end with a ``:`` character.

**Lines 6 → 9**
   We define the value of the ``dynamic`` key. This value **must** be
   a list.

   The items in the list are ``arg_name_0``, ``arg_name_1``, and
   ``arg_name_1``. Each of these is the name of a dynamic variable
   required by **worker:Function**.

----

Applying this example to our fictional situation will yield the
following playbook:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 9,10,11

   ---
   group: inception
   name: Simple playbook
   execution:
     - description: restart httpd
       hosts:
         - foo.bar.example.com
       steps:
         - change:Update:
             dynamic:
               - id
         - service:Restart: {service: httpd}

**Line 9**
   Insert the new sequence step, ``change:Update``

**Line 10**
   Begin the ``dynamic`` argument dictionary

**Line 11**
   Define the dynamic argument list with one item: ``id``


Providing Values
||||||||||||||||

Now that we have learned how to add a sequence step that requires
dynamic arguments to a playbook, it might be helpful to quickly review
how clients can provide the information.

A commonly used command line tool for interacting with REST endpoints
(such as :ref:`RE-REST <re_rest>`) is ``curl``. Put simply, curl
allows you to make a request to anything ``http``. This is exactly
like following a hyperlink in a web page.

The following is an example of how to use the curl command to provide
the value of the dynamic argument, ``id``, to the release engine and
start a deployment.

.. code-block:: bash
   :linenos:
   :emphasize-lines: 2

   $ curl -u "user:passwd" -H "Content-Type: application/json" \
   -d '{"id": "CHG1337"}' \
   -X PUT \
   http://rerest.example.com/api/v0/test/playbook/12345/deployment/

Line **2** utilizes the ``-d`` (or ``--data``) option to provide the
value of the dynamic argument. When curl is ran in this manner,
dynamic arguments are provided by describing a dictionary including
`key-value pairs` where the key is the dynamic argument name, and the
value is the unique-value of that argument for this particular
deployment. In this example the dictionary is ``{"id": "CHG1337"}``.


.. seealso::

   RE-REST - Dynamic Variables
      See the :ref:`RE-REST → dynamic variables
      <rerest_dynamic_variables>` documentation for a complete review
      of this topic.
