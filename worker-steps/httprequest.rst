.. _steps_httprequest:

HTTPRequest
***********

httprequest:Get
===============

Executes an HTTP GET on a URL.

**Parameters**

* ``url`` (type ``str``)

  * **Required:** True
  * **Description:** The URL to attempt to get.


* ``code`` (type ``int``)

  * **Required:** False
  * **Description:** The expected status code. Default: 200

**Example**

To request http://example.com and expect a 200:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3-5

   hosts: ['localhost']
   steps:
       - httprequest:Get:
           url: "http://example.com"
           code: 200

httprequest:Put
===============

Executes an HTTP PUT on a URL.

**Parameters**

* ``url`` (type ``str``)

  * **Required:** True
  * **Description:** The URL to attempt to put.

* ``content`` (type ``str``)

  * **Required:** True
  * **Description:** The content itself.

* ``contenttype`` (type ``str``)

  * **Required:** True
  * **Description:** The content type of the content.

* ``b64encoded`` (type ``bool``)

  * **Required:** False
  * **Description:** Set to true if the content is base64encoded. The worker will then decode before PUTting.

* ``code`` (type ``int``)

  * **Required:** False
  * **Description:** The expected status code. Default: 200

**Example**

To PUT json to http://example.com and expect a 201:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3-7

   hosts: ['localhost']
   steps:
       - httprequest:Put:
           url: "http://example.com"
           code: 201
           contenttype: "application/json"
           content: "{}"

httprequest:Post
================

Executes an HTTP POST on a URL.

**Parameters**

* ``url`` (type ``str``)

  * **Required:** True
  * **Description:** The URL to attempt to post.

* ``content`` (type ``str``)

  * **Required:** True
  * **Description:** The content itself.

* ``contenttype`` (type ``str``)

  * **Required:** True
  * **Description:** The content type of the content.

* ``b64encoded`` (type ``bool``)

  * **Required:** False
  * **Description:** Set to true if the content is base64encoded. The worker will then decode before POSTing.

* ``code`` (type ``int``)

  * **Required:** False
  * **Description:** The expected status code. Default: 200

**Example**

To POST json to http://example.com and expect a 200:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3-7

   hosts: ['localhost']
   steps:
       - httprequest:Post:
           url: "http://example.com"
           code: 200
           contenttype: "application/json"
           content: "{}"

httprequest:Delete
==================

Executes an HTTP DELETE on a URL.

**Parameters**

* ``url`` (type ``str``)

  * **Required:** True
  * **Description:** The URL to attempt to delete.


* ``code`` (type ``int``)

  * **Required:** False
  * **Description:** The expected status code. Default: 200

**Example**

To request a DELETE on http://example.com and expect a 410:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3-5

   hosts: ['localhost']
   steps:
       - httprequest:Delete:
           url: "http://example.com"
           code: 410
