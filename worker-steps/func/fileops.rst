.. _steps_fileops_puppet:

Fileops
=======

The **fileops** module allows you to interact with the files on remote hosts.
This is meant to be slightly more restricted than allowing puppet:Run.

fileops:ChangeOwnership
-----------------------

**Parameters**

* ``path`` (type: str or list)

  * **Required:** True
  * **Default:** ``None``
  * **Description:** Path(s) to target files and/or directories

* ``user`` (type: str)

  * **Required:** True
  * **Default:** ``None``
  * **Description:** The user who will own the file(s)/dir(s)

* ``group`` (type: str)

  * **Required:** True
  * **Default:** ``None``
  * **Description:** The group who will own the file(s)/dir(s)

* ``recursive`` (type: bool)

  * **Required:** False
  * **Default:** ``False``
  * **Description:** If the command should traverse through directories


**Example**

.. code-block:: yaml
   :linenos:

   hosts: ['localhost']
   steps:
       # Change ownership of one file
       - fileops:ChangeOwnership:
           path: /tmp/filename
           user: root
           group: root

       # Change ownership of an entire directory
       - fileops:ChangeOwnership:
           path: /tmp/dir/
           user: root
           group: root
           recursive: true

       # Change ownership of an entire directory and a file
       - fileops:ChangeOwnership:
           path:
              - /tmp/dir/
              - /tmp/filename
           user: root
           group: root
           recursive: true


fileops:ChangePermissions
-------------------------

**Parameters**

* ``path`` (type: str)

  * **Required:** True
  * **Default:** ``None``
  * **Description:** Path to a specific file or dir

* ``mode`` (type: str)

  * **Required:** True
  * **Default:** ``None``
  * **Description:** The new mode. Ex: 0644

* ``recursive`` (type: bool)

  * **Required:** False
  * **Default:** ``False``
  * **Description:** If the command should traverse through directories


**Example**

.. code-block:: yaml
   :linenos:

   hosts: ['localhost']
   steps:
       # Change permissions on one file
       - fileops:ChangePermissions:
           path: /tmp/filename
           user: root
           group: root

       # Change permissions on an entire directory
       - fileops:ChangePermissions:
           path: /tmp/dir/
           user: root
           group: root
           recursive: true


fileops:FindInFiles
-------------------

**Parameters**

* ``path`` (type: str or list)

  * **Required:** True
  * **Default:** ``None``
  * **Description:** Path(s) to target files and/or directories

* ``regexp`` (type: str)

  * **Required:** True
  * **Default:** ``None``
  * **Description:** Regular expression to search with

* ``case_insensitive`` (type: bool)

  * **Required:** False
  * **Default:** ``False``
  * **Description:** Makes the search case insensitive

* ``recursive`` (type: bool)

  * **Required:** False
  * **Default:** ``False``
  * **Description:** If the command should traverse through directories


**Example**

.. code-block:: yaml
   :linenos:

   hosts: ['localhost']
   steps:
       # Search a file for the string "test"
       - fileops:FindInFiles:
           path: /tmp/filename
           regexp: test

       # Search all files in a dir for the string "test"
       - fileops:FindInFiles:
           path: /tmp/dir/
           regexp: test
           recursive: true

       # Search a file for the string "test" in any case
       - fileops:FindInFiles:
           path: /tmp/filename
           regexp: test
           case_insensitive: true


fileops:Move
------------

**Parameters**

* ``path`` (type: str or list)

  * **Required:** True
  * **Default:** ``None``
  * **Description:** Path(s) to target files and/or directories

* ``to`` (type: str)

  * **Required:** True
  * **Default:** ``None``
  * **Description:** The location for the target path


**Example**

.. code-block:: yaml
   :linenos:

   hosts: ['localhost']
   steps:
       # Rename a filename
       - fileops:Move:
           path: /tmp/filename
           to: /tmp/newname

       # Move files to a new directory
       - fileops:Move:
           path:
              - /tmp/filename
              - /tmp/newname
           to: /tmp/dir/


fileops:Remove
--------------

**Parameters**

* ``path`` (type: str or list)

  * **Required:** True
  * **Default:** ``None``
  * **Description:** Path(s) to target files and/or directories

* ``recursive`` (type: bool)

  * **Required:** False
  * **Default:** ``False``
  * **Description:** If the command should traverse through directories


**Example**

.. code-block:: yaml
   :linenos:

   hosts: ['localhost']
   steps:
       # Remove a file
       - fileops:Remove:
           path: /tmp/filename

       # Remove a directory
       - fileops:Remove:
           path: /tmp/dir/
           recursive: true

fileops:Touch
-------------

**Parameters**

* ``path`` (type: str or list)

  * **Required:** True
  * **Default:** ``None``
  * **Description:** Path(s) to target files to create


**Example**

.. code-block:: yaml
   :linenos:

   hosts: ['localhost']
   steps:
       # Create a single, empty file
       - fileops:Touch:
           path: /tmp/filename

       # Create multiple empty files
       - fileops:Touch:
           path:
              - /tmp/filename
              - /tmp/newname

fileops:Tar
-----------

**Parameters**

* ``path`` (type: str or list)

  * **Required:** True
  * **Default:** ``None``
  * **Description:** Path(s) to target files/dirs to include

* ``to`` (type: str)

  * **Required:** True
  * **Default:** ``None``
  * **Description:** The path of the new archive

* ``compression`` (type: str)

  * **Required:** False
  * **Default:** ``None``
  * **Description:** gzip or bzip



**Example**

.. code-block:: yaml
   :linenos:

   hosts: ['localhost']
   steps:
       # Create an archive of one file
       - fileops:Tar:
           path: /tmp/filename
           to: /tmp/filename.tar

       # Create a compressed tar of a directory
       - fileops:Tar:
           path: /tmp/dir/
           to: /tmp/dir.tar.gz
           compression: gzip

       # Create a compressed tar of a directory and specific files
       - fileops:Tar:
           path:
               - /tmp/dir/
               - /tmp/filename
               - /tmp/newfile
           to: /tmp/dir.tar.gz
           compression: bzip
