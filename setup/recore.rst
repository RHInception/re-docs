RE-CORE
~~~~~~~
The core is essentially a finite state machine (FSM) hooked into a message bus and a database.

The core oversees the execution of all release steps for any given project. The core is separate from the actual execution of each release step. Execution is delegated to the worker components.

Setup Steps
```````````
* Provision or choose a server to utilize for RE-CORE
* Install Python v2
* Install the python v2 libraries `listed here <https://github.com/RHInception/re-core/blob/master/requirements.txt>`_.
* Follow the RE-CORE configuration instructions at :ref:`recore-conf`.
* Choose and implement a RE-CORE deployment strategy via :ref:`recore-deployment`.

Test Setup
``````````
.. todo::
   How to verify it's ready.
