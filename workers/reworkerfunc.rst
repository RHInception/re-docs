RE-WORKER-FUNC
--------------
Func Worker for Release Engine.

.. todo::
   Explain better what this is used for.

Configuration
~~~~~~~~~~~~~
The configuration file uses the following pattern in JSON format:

.. code-block:: json

   {
       "FUNC_MODULE": {
           "COMMAND_1": ["REQUIRED", "PARAMETERS"],
           "COMMAND_2": ["ONE_ITEM"],
           "COMMAND_N": []
   }

Example
```````
.. code-block:: json

   {
       "yumcmd": {
           "install": ["package"],
           "remove": ["package"],
           "update": []
       }
   }

Unittests
~~~~~~~~~
Run ``make tests`` from the main directory to execute unittests (including `pep8 <https://pypi.python.org/pypi/pep8>`_ and `pyflakes <https://pypi.python.org/pypi/pyflakes>`_)


Engine Step Example
~~~~~~~~~~~~~~~~~~~

The following is just a *snippet* of a complete project.

.. code-block:: json

   {
      "steps": [
          {
              "name": "stop foo service",
              "plugin": "funcworker",
              "parameters": {
                  "command": "service",
                  "subcommand": "stop",
                  "service": "megafrobber",
                  "hosts": ["127.0.0.1"]
              }
          }
      ]
   }

Notes
~~~~~
 See `Func - Module List <https://fedorahosted.org/func/wiki/ModulesList>`_ for more information.
