RE-CORE
-------
The core is essentially a finite state machine (**FSM**) hooked into a message bus and a database.

The core oversees the execution of all *release steps* for any given project. The core is separate from the actual execution of each release step. Execution is delegated to the **worker** component.


Running From Source
~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

   $ . ./hacking/setup-env
   $ re-core -c ./examples/settings-example.json


.. _recore-conf:

RE-CORE Configuration
~~~~~~~~~~~~~~~~~~~~~
Configuration of the server is done in JSON. You can find an example configuration file in the `examples/` directory.

You can point to a specific configuration file using the `-c` command-line option.

========== ====== ======== ===========================================
Name       Type   Parent   Value                                      
========== ====== ======== ===========================================
LOGFILE    str    None     File name for the application level log    
MQ         dict   None     Where all of the MQ connection settings are
SERVER     str    MQ       Hostname or IP of the server               
NAME       str    MQ       Username to connect with                   
PASSWORD   str    MQ       Password to authenticate with              
QUEUE      str    MQ       Queue on the server to bind                
DB         dict   None     Where all the DB connection settings are   
SERVERS    list   DB       List of all of the MongoDB hostname/IPs    
DATABASE   str    DB       Name of the MongoDB database               
NAME       str    DB       Username to connect with                   
PASSWORD   str    DB       Password to authenticate with              
========== ====== ======== ===========================================

For an example see `example-config.json <https://github.com/RHInception/re-core/blob/master/examples/settings-example.json>`_.


.. _recore-deployment:

RE-CORE Deployment
~~~~~~~~~~~~~~~~~~
.. todo::
   How does one deploy this?
