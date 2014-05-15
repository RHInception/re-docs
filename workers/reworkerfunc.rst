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


**Note:** See
`Func - Module List <https://fedorahosted.org/func/wiki/ModulesList>`_ for more information.
