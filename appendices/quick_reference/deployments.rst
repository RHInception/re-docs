.. _quick_reference_deployments:

Deployments
===========

Quick reference for deployment commands.

Assume:

* A playbook exists with ID ``54219f1702b67c666dde3a7d``
* Our **re-rest** base endpoint is ``http://rerest.example.com/api/v0/``


Start (No dynamic args)
-----------------------

Example of how to start a deployment which requires no dynamic arguments
and authentication required.

.. code-block:: bash
   :linenos:
   :emphasize-lines: 3

   $ curl -v -X PUT -u $USER  -H "Content-Type: application/json" https://rerest.example.com/api/v0/test/playbook/54219f1702b67c666dde3a7d/deployment/
   {
     "id": "542ac6607d4b6d00153b6db5",
     "status": "created"
   }

Our deployment ID is on line **3**: ``542ac6607d4b6d00153b6db5``

Start (With dynamic args)
-------------------------

Example of how to start a deployment which requires dynamic arguments and
authentication required.

.. code-block:: bash
   :linenos:
   :emphasize-lines: 3

   curl -v -X PUT -u $USER -d '{"environment": "qa", "change_record": "CHG0002025"}' -H "Content-Type: application/json" https://rerest.example.com/api/v0/test/playbook/54219f1702b67c666dde3a7d/deployment/
   {
     "id": "542ac6607d4b6d00153b6db6",
     "status": "created"
   }

Our deployment ID is on line **3**: ``542ac6607d4b6d00153b6db6``
