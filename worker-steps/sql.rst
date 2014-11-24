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
                   length: 255
                   primary_key: true
               col2:
                   type: String
                   length: 255


sql:DropTable
=============

Drops a table from a database.

**Parameters**

* ``database`` (type ``str``)

  * **Required:** True
  * **Description:** The name of the database to operate on


* ``name`` (type ``str``)

  * **Required:** True
  * **Description:** The table to drop.


**Example**

To drop ``MyTable``:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3,6

   hosts: ['localhost']
   steps:
       - sql:DropTable:
           datbase: a_configured_database
           name: MyTable


sql:AlterTableColumns
=====================

Alters one or more columns in a table.

**Parameters**

* ``database`` (type ``str``)

  * **Required:** True
  * **Description:** The name of the database to operate on


* ``name`` (type ``str``)

  * **Required:** True
  * **Description:** The table name.

* ``columns`` (type ``dict``)

  * **Required:** True
  * **Description:** Explination of how the columns should look
    like. For more information on types see `SQL Alchemy - Generic
    Types
    <http://docs.sqlalchemy.org/en/rel_0_8/core/types.html#types-generic>`_
    documentation.


**Example**

To alter ``MyTable`` modifying a column:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3,6

   hosts: ['localhost']
   steps:
       - sql:AlterTableColumns:
           datbase: a_configured_database
           name: MyTable
           columns:
               col_to_modify:
                   type: String
                   length: 255


sql:AddTableColumns
===================

Adds one or more columns in a table.

**Parameters**

* ``database`` (type ``str``)

  * **Required:** True
  * **Description:** The name of the database to operate on


* ``name`` (type ``str``)

  * **Required:** True
  * **Description:** The table name.

* ``columns`` (type ``dict``)

  * **Required:** True
  * **Description:** Explination of how the columns should look
    like. For more information on types see `SQL Alchemy - Generic
    Types
    <http://docs.sqlalchemy.org/en/rel_0_8/core/types.html#types-generic>`_
    documentation.


**Example**

To add two new columns to ``MyTable``:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3,6

   hosts: ['localhost']
   steps:
       - sql:AddTableColumns:
           datbase: a_configured_database
           name: MyTable
           columns:
               new_col:
                   type: String
                   length: 255
               another_new_col:
                   type: Integer

sql:DropTableColumns
====================

Drops one or more columns from a table.

**Parameters**

* ``database`` (type ``str``)

  * **Required:** True
  * **Description:** The name of the database to operate on


* ``name`` (type ``str``)

  * **Required:** True
  * **Description:** The table name.

* ``columns`` (type ``list``)

  * **Required:** True
  * **Description:** The names of the columns to drop

**Example**

Drop col2 from ``MyTable``:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3,6

   hosts: ['localhost']
   steps:
       - sql:DropTableColumns:
           datbase: a_configured_database
           name: MyTable
           columns: [
               col2
           ]

sql:Insert
==========

Inserts one or more rows into a table.

**Parameters**

* ``database`` (type ``str``)

  * **Required:** True
  * **Description:** The name of the database to operate on


* ``name`` (type ``str``)

  * **Required:** True
  * **Description:** The table name.

* ``rows`` (type ``list`` io ``dict``)

  * **Required:** True
  * **Description:** Explination of the data to insert


**Example**

To insert two rows into ``MyTable``:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3,6

   hosts: ['localhost']
   steps:
       - sql:Insert:
           datbase: a_configured_database
           name: MyTable
           rows: [
               {
                   col1: "hi",
                   col2: "hello"
               },
               {
                   col1: "sup",
                   col2: "aloha"
               }
           ]


sql:Delete
==========

Deletes one or more rows from a table.

.. note::
   Currently Delete only supports equals in where.

**Parameters**

* ``database`` (type ``str``)

  * **Required:** True
  * **Description:** The name of the database to operate on


* ``name`` (type ``str``)

  * **Required:** True
  * **Description:** The table name.

* ``where`` (type ``dict``)

  * **Required:** True
  * **Description:** Explination of when to delete.


**Example**

Delete everything from ``MyTable`` where col1 is hi:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3,6

   hosts: ['localhost']
   steps:
       - sql:Delete:
           datbase: a_configured_database
           name: MyTable
           where: {
               col1: "hi"
           }


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
   :emphasize-lines: 4,5

   hosts: ['localhost']
   steps:
       - sql:ExecuteSQL:
           database: a_configured_database
           sql: 'INSERT INTO myTable (col1, col2) VALUES ("hello", "there")'
