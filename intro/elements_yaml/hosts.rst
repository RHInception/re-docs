.. _hosts:

Hosts
^^^^^

* Required: Yes
* Argument type: List
* Default: None

The ``hosts`` key is used to describe the target hosts for the script to
act on. The ``hosts`` key takes a list of hosts as input. 
can specify hosts as shell-like globs

.. code-block:: yaml

    hosts:
      - www01.web.ext.example.com
      - www02.web.ext.example.com
      - www03.web.ext.example.com
