.. _yaml_hosts:

Hosts
^^^^^

* Required: Yes
* Argument type: List
* Default: None

The ``hosts`` element is used to describe the target hosts for the
script to act on.

.. code-block:: yaml

   ---
   - hosts:
      - www01.web.ext.example.com
      - www02.web.ext.example.com
      - www03.web.ext.example.com

Or in YAML shorthand:

.. code-block:: yaml

   ---
   - hosts: [www01.web.ext.example.com, www02.web.ext.example.come, www03.web.ext.example.com]
