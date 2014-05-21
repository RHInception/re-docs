.. _yaml_preflight:

Preflight
^^^^^^^^^

* Required: No
* Argument type: List
* Default: None

The ``preflight`` element defines the steps that will be performed on
each host in :ref:`hosts <yaml_hosts>` before the :ref:`yaml_steps`
body executes. The syntax of each possible step varries. All of the
steps are documented in the :ref:`Components → Pre-Built Workers
<components_pre_built>` section.

The preflight block is executed concurrently against all ``hosts``
given. When the ``preflight`` block finishes the Release Engine will
pause and wait for confirmation before continuing.

.. admonition:: Difference from Taboot

   Those familiar with `Taboot <https://fedorahosted.org/Taboot>`_
   should note an important difference here: Currently, RE will
   **not** pause between running the ``preflight`` steps and the
   ``steps`` steps.
