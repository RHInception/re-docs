.. _steps_git:

Git
***

git:Merge
=========
Merges one branch into another and then pushes it back to origin.

**Parameters**

* ``repo`` (type ``str``)

  * **Required:** True
  * **Description:** Git repo to clone


* ``to_branch`` (type ``str``)

  * **Required:** True
  * **Description:** The branch to merge in to

* ``from_branch`` (type ``str``)

  * **Required:** False
  * **Description:** The branch to merge from


**Example**

To merge one commit into mybranch:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3,6

   hosts: ['localhost']
   steps:
       - git:Merge:
           repo: git@example.com/someplace.git
           to_branch: master
           from_branch: feature


git:CherryPickMerge
===================

Cherry pick's commits from one branch and then squashs the results into another.

**Parameters**

* ``repo`` (type ``str``)

  * **Required:** True
  * **Description:** Git repo to clone


* ``to_branch`` (type ``str``)

  * **Required:** True
  * **Description:** The branch to squash merge in to

* ``temp_branch`` (type ``str``)

  * **Required:** False
  * **Description:** The temporary branch to use while cherry picking (Default: mergebranch)

* ``commits`` (type ``list``)

  * **Required:** True
  * **Description:** List of commits to cherry pick

* ``run_scripts`` (type ``list``)

  * **Required:** False
  * **Description:** List of script names to execute before pushing results back to server


**Example**

To merge one commit into mybranch:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 3,6

   hosts: ['localhost']
   steps:
       - git:CherryPickMerge:
           repo: git@example.com/someplace.git
           to_branch: mybranch
           commits: ['77a085eb34597b544a233d8ce4c943dcac']
