.. _appendicies_quick_reference_crud:

Playbook CRUD
=============

Quick reference for creating, reading, updating, and deleting
playbooks. Examples provided for both JSON and YAML input/outputs.

Assume the following:

* A playbook not uploaded yet in JSON format: **pb.json**
* A playbook not uploaded yet in YAML format: **pb.yaml**
* The ID of an already uploaded playbook: ``54219f1702b67c666dde3a7d``
* Authentication is required
* We are a member of the group called: **crudtest**
* Our **re-rest** base endpoint is ``http://rerest.example.com/api/v0/``


.. _appendicies_quick_reference_crud_create:

Create
------

**JSON**

.. code-block:: bash

   $ curl -X PUT -u $USER --data @pb.json -H "Content-Type: application/json" http://rerest.example.com/api/v0/crudtest/playbook/


**YAML**

.. code-block:: bash

   $ curl -X PUT -u $USER --data-binary @pb.yaml -H "Content-Type: application/yaml" http://rerest.example.com/api/v0/crudtest/playbook/?format=yaml


.. _appendicies_quick_reference_crud_read:

Read
----

.. note:: The READ (``GET``) operations do not require explicitly
          setting the ``Content-Type`` header.

**JSON**

.. code-block:: bash

   # Get a single playbook from the 'crudtest' group collection
   $ curl -X GET -u $USER http://rerest.example.com/api/v0/crudtest/playbook/54219f1702b67c666dde3a7d/

   # Get ALL playbooks for the 'crudtest' group collection in JSON format
   $ curl -X GET -u $USER http://rerest.example.com/api/v0/test/playbook/

**YAML**

.. code-block:: bash

   # Get a single playbook from the 'crudtest' group collection
   $ curl -X GET -u $USER http://rerest.example.com/api/v0/crudtest/playbook/54219f1702b67c666dde3a7d/?format=yaml

   # Get ALL playbooks for the 'crudtest' group collection in YAML format
   $ curl -X GET -u $USER http://rerest.example.com/api/v0/test/playbook/?format=yaml


.. _appendicies_quick_reference_crud_update:

Update
------

**JSON**

.. code-block:: bash

   $ curl -X POST -u $USER -H "Content-Type: application/json" --data @pb.json http://rerest.example.com/api/v0/crudtest/playbook/54219f1702b67c666dde3a7d/


**YAML**

.. code-block:: bash

   $ curl -X POST -u $USER -H "Content-Type: application/yaml" --data-binary @pb.yaml http://rerest.example.com/api/v0/crudtest/playbook/54219f1702b67c666dde3a7d/?format=yaml



.. _appendicies_quick_reference_crud_delete:

Delete
------

.. note:: The ``DELETE`` action is JSON/YAML agnostic.

.. code-block:: bash

   $ curl -X DELETE -u $USER http://rerest.example.com/api/v0/crudtest/playbook/54219f1702b67c666dde3a7d/
