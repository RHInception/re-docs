.. _re_cfg_seed:

RE-CFG-SEED
-----------
A simple confugration managemetn solution which uses `etcd <https://github.com/coreos/etcd>`_ which attempts to pull
configuration paramteres from the cluster. If the parameters do not exist sane defaults are used. If the passwords
are not defined randomly generated passwords are created for the accounts.

.. contents:: Table of Contents
   :depth: 3

.. _re_cfg_seed_desc:

Purpose of RE-CFG-SEED
~~~~~~~~~~~~~~~~~~~~~~
The purpose of re-cfg-seed is to make setting up a brand new instance of Winternewt as easy as possible. The idea is
that most of the confugration values can either have sane defaults or, in the case of passwords, be randomly generated
and used within the system. This decreases the amounf of upfront set up required to run Winternewt. However, if
the administrator(s) of the instance want to specify different ports/passwords/etc.. they are free to do so by
inserting the information into etcd or pointing re-cfg-seed (or the internal etcd server) to their own etcd server.


.. _re_cfg_seed_conf:

RE-CFG-SEED Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~

Configuration of re-cfg-seed is done with a json file. This file describes what configuration variables need to be
either retrieved from etcd or generated and published into etcd in the case of variables not existing. You can see
an example configuration file `here <https://github.com/RHInception/re-cfg-seed/blob/master/conf/conf.json>`_


================================== ====== =================== ===========================================
Name                               Type   Parent              Value
================================== ====== =================== ===========================================
endpoint                           str    None                Full URL to the etcd endpoint. Default is http://127.0.0.1:4001
keys                               dict   None                Name of the parameters to look for or seed if empty.
$key                               dict   keys                Structure explaining basic information about the key. Takes: default and password.
================================== ====== =================== ===========================================


Example:

.. code-block:: json
   :linenos:
   :emphasize-lines: 2,4-6

   {
       "endpoint": "http://127.0.0.1:4001",
       "keys": {
           "cats": {"default": "thisshouldnotchange"},
           "hammer": {"default": "time"},
           "password": {"password": true}
       }
   }


What's Happening
~~~~~~~~~~~~~~~~
* re-cfg-seed reads it's configuration file to find out what endpoint and keys it should look for
* For each key re-cfg-seed makes a REST call to etcd asking for the value for the key
* If the value exists ...

 * re-cfg-seed uses the value it received.

* If the value does not exist in etcd ...

 * re-cfg-seed looks at it's key structure ...

  * If default is set re-cfg-seed sends the default up to etcd and uses said default as it's value
  * If password is set to true (and default is not set) a random password is generated, sent to etcd and used as the value
  * If neither default or password are set then an error occurs

* re-cfg-seed opns up the output configuration file and updates/creates all relevent keys with their updated values


Usage Example
~~~~~~~~~~~~~


.. code-block:: bash

   $ re-cfg-seed --help
   usage: re-cfg-seed [-h] CONF OUT
   
   positional arguments:
     CONF        Path to the configuration file
     OUT         Path to the json file to write values to.
   
   optional arguments:
     -h, --help  show this help message and exit
   
   $ re-cfg-seed conf/re-worker-output.conf /configs/workers/re-worker-output.json
   $ echo $?
   0
   $

