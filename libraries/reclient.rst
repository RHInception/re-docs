RE-CLIENT
---------
Release Engine - Client Tool

.. todo::
   Define what this is better.


Running From Checkout
~~~~~~~~~~~~~~~~~~~~~

.. todo::
   How do they install it?

.. code-block:: bash

   $ export PYTHONPATH=`pwd`/src/:$PYTHONPATH
   $ ./bin/re-client

At this point you'll be prompted to enter some configuration values::

   Could not load base rerest url from /home/tbielawa/.reclient.conf
   Enter the hostname of your rerest endpoint
   This will be saved in /home/tbielawa/.reclient.conf for reuse later
   Hostname:

At this point you would enter the hostname of your
`re-rest <https://github.com/RHInception/re-rest>`_ endpoint.
::

   Hostname: rerest.example.com

   0) Get all playbooks ever
   1) Get all playbooks for a project
   2) Get a single playbook for a project
   3) Update a playbook
   4) Delete a playbook
   5) Create a new playbook
   6) Quit
   command>>

Release Engine Client Tools are now setup and ready to use.

Command Line Options
~~~~~~~~~~~~~~~~~~~~

The ``re-client`` command accepts two optional parameters:

* ``--project``, ``-p`` - Set the default project
* ``--id``, ``-i`` - Set the default playbook ID

Usage Example
`````````````
Let's work with **example project** for the duration of this session:

.. code-block:: bash

   $ ./bin/re-client -p 'example project'


Notes
~~~~~
The REPL (command loop) has **readline** history enabled. This means
the up/down arrow keys work and you can edit lines of input **like a
boss**.
