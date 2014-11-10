.. _steps_sql:

SQL
***

sql:CreateTable
===============

Creates a NEW database table.

**Parameters**

* ``database`` (type ``str``)

  * **Required:** True
  * **Description:** The name of the database to operate on


* ``name`` (type ``str``)

  * **Required:** True
  * **Description:** The new tables name.

* ``columns`` (type ``dict``)

  * **Required:** True
  * **Description:** Explination of how the table should look
    like. For more information on types see `SQL Alchemy - Generic
    Types
    <http://docs.sqlalchemy.org/en/rel_0_8/core/types.html#types-generic>`_
    documentation.


**Example**

To create a table named ``MyTable`` with a two ``varchar`` columns:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3,6

   hosts: ['localhost']
   steps:
       - sql:CreateTable:
           datbase: a_configured_database
           name: MyTable
           columns:
               col1:
                   type: String
                   primary_key: true
               col2:
                   type: String


sql:ExecuteSQL
==============

Execute raw (and database specific) SQL.

**Parameters**

* ``database`` (type ``str``)

  * **Required:** True
  * **Description:** The name of the database to operate on


* ``sql`` (type ``str``)

  * **Required:** True
  * **Description:** The SQL to execute.


**Example**

To insert a row in to ``MyTable``:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3,6

   hosts: ['localhost']
   steps:
       - sql:ExecuteSQL:
           datbase: a_configured_database
           sql: 'INSERT INTO myTable (col1, col2) VALUES ("hello", "there")'
