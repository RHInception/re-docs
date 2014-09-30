.. _re_rest:

RE-REST
-------
Simple `REST Api
<http://en.wikipedia.org/wiki/Representational_state_transfer>`_ for
the Release Engine. By design RE-REST is the only way to interact with
the Release Engine.

.. contents:: Table of Contents
   :depth: 3

.. _rerest_conf:

RE-REST Configuration
~~~~~~~~~~~~~~~~~~~~~


Configuration of the server is done in JSON and is by default kept in
the current directories `settings.json
<https://github.com/RHInception/re-rest/blob/master/example-settings.json>`_
file.

You can override the location by setting ``REREST_CONFIG`` environment variable.


================================== ====== =================== ===========================================
Name                               Type   Parent              Value
================================== ====== =================== ===========================================
LOGFILE                            str    None                File name for the application level log
LOGLEVEL                           str    None                DEBUG, INFO (default), WARN, FATAL
MQ                                 dict   None                Where all of the MQ connection settings are
SERVER                             str    MQ                  Hostname or IP of the server
PORT                               int    MQ                  Port to connect on
USER                               str    MQ                  Username to connect with
PASSWORD                           str    MQ                  Password to authenticate with
VHOST                              str    MQ                  vhost on the server to utilize
MONGODB_SETTINGS                   dict   None                Where all of the MongoDB settings live
DB                                 str    MONGODB_Settings    Name of the database to use
USERNAME                           str    MONGODB_Settings    Username to auth with
Password                           str    MONGODB_Settings    Password to auth with
HOST                               str    MONGODB_Settings    Host to connect to
PORT                               int    MONGODB_Settings    Port to connect to on the host
PLAYBOOK_UI                        bool   None                Turn's on/off the experimental playbook ui. It's off by default.
AUTHORIZATION_CALLABLE             str    None                module.location:callable. Eg: ``rerest.authorization:no_authorization``
AUTHORIZATION_ENVIRONMENT_CALLABLE str    None                module.location:callable that decides is a user can modify an environment
AUTHORIZATION_CONFIG               dict   None                Authorization callable specific configuration items
GROUP_ENVIRONMENT_MAPPING          dict   None                Mapping of group: ["allowed", "environments"].
================================== ====== =================== ===========================================


Further configuration items can be found in the `Flask Documentation
<http://flask.pocoo.org/docs/config/#builtin-configuration-values>`_
or look at specific ``AUTHORIZATION_CALLABLE``/``AUTHORIZATION_ENVIRONMENT_CALLABLE`` documentation.

For an example see `example-settings.json <http://github.com/RHInception/re-rest/blob/master/example-settings.json>`_


Authentication
~~~~~~~~~~~~~~
re-rest uses a simple decorator which enforces a ``REMOTE_USER`` be set.

rerest.decorators:remote_user_required
``````````````````````````````````````

This decorator assumes that re-rest is running behind another web
server which is taking care of authentication. If ``REMOTE_USER`` is
passed to re-rest from the web server re-rest assumes authentication
has succeeded. If it is not passed through re-rest treats the users as
unauthenticated.

.. warning::
   When using this decorator it is very important that re-rest not be reachable by any means other than through the front end webserver!!

Authorization: Callable
~~~~~~~~~~~~~~~~~~~~~~~
re-rest uses two decorators. The first keys off the ``AUTHORIZATION_CALLABLE`` configuration parameter. This callable
is responsible for deciding if a user has access to the URL in question.

rerest.authorization.no_authorization
`````````````````````````````````````
.. warning::
   This should not be used in a production environment

To use this callable set ``AUTHORIZATION_CALLABLE`` to ``rerest.authorization:no_authorization``.


rerest.authorization.ldap_search
````````````````````````````````

To use this callable set ``AUTHORIZATION_CALLABLE`` to ``rerest.authorization:ldap_search`` and set the following items
in your configuration file.

=============================== ====== ====================== ================================================
Name                            Type   Parent                 Value
=============================== ====== ====================== ================================================
LDAP_URI                        str    AUTHORIZATION_CONFIG   A full ldap URI such as ``ldaps://127.0.0.1``
LDAP_USER                       str    AUTHORIZATION_CONFIG   User to bind with
LDAP_PASSWORD                   str    AUTHORIZATION_CONFIG   Password to bind with
LDAP_SEARCH_BASE                str    AUTHORIZATION_CONFIG   Search base for all queries. Ex: ``dc=example,dc=com``
LDAP_MEMBER_ID                  str    AUTHORIZATION_CONFIG   The name of the field that houses the username
LDAP_FIELD_MATCH                str    AUTHORIZATION_CONFIG   What field to use against the lookup table
LDAP_LOOKUP_TABLE               dict   AUTHORIZATION_CONFIG   key: list table of ``LDAP_FIELD_MATCH`` items to allowed groups. A ``*`` means all groups.
LDAP_GROUP_ENVIRONMENT_MAPPING  dict   AUTHORIZATION_CONFIG   key: list table of ``LDAP_FIELD_MATCH`` items to allowed environments.
=============================== ====== ====================== ================================================

Here's a command-line example of how the ``LDAP_LOOKUP_TABLE``
property is used. In this example we will learn how authorization of
the user **testuser** is determined.

Our organization has an ldap server at **ldap.example.com**, and
groups are organized under the **ou=Groups,dc=example,dc=com**
sub-tree. In this example re-rest will not attempt to **bind**
(authenticate) with the LDAP server. Here is an example of this
configuration:

.. code-block:: json
   :linenos:
   :emphasize-lines: 10

    {
        "AUTHORIZATION_CONFIG": {
            "LDAP_URI": "ldap://ldap.example.com",
            "LDAP_USER": "",
            "LDAP_PASSWORD": "",
            "LDAP_SEARCH_BASE": "ou=Groups,dc=example,dc=com",
            "LDAP_MEMBER_ID": "memberUid",
            "LDAP_FIELD_MATCH": "cn",
            "LDAP_LOOKUP_TABLE": {
                "admins": ["prod"],
                "superadmins": ["*"]
            },
            "LDAP_GROUP_ENVIRONMENT_MAPPING": {
                "someldapgroup": ["dev", "qa"],
                "superadmins": ["dev", "qa", "stage", "production"]
            }
        }
   }

The **admins** group could look like this:

.. code-block:: console
   :linenos:
   :emphasize-lines: 6

   dn: cn=admins,ou=Groups,dc=example,dc=com
   cn: admins
   objectClass: top
   objectClass: posixGroup
   gidNumber: 1337
   memberUid: testuser
   memberUid: testboss

On line **6** we can see that this user is a member of the LDAP group
**admins**. We also see here that group membership is denoted by use
of the ``memberUid`` attribute. Note how this matches the the
``LDAP_MEMBER_ID`` setting we previously mentioned.

Let's pretend **testuser** is attempting to run a playbook with the
:ref:`group <elements_yaml>` field set to **prod** (short for
**production**). To determine authorization, **re-rest** will perform
an `LDAP search <https://www.ietf.org/rfc/rfc2254.txt>`_ to query for
records which match **two** conditions:

#. A record for a group exists in the ``ou=Groups,dc=example,dc=com``
   sub-tree with a ``cn`` of **admins**
#. The discovered record has a ``memberUid`` attribute which matches
   the user's name: **testuser**

In LDAP search filter syntax, this query would look like the following::

   (&(cn=admins)(memberUid=testuser))

With the ``ldapsearch`` command-line tool, we can test this
authorization with the following command:

.. code-block:: console

   $ ldapsearch -xLLL -b ou=Groups,dc=example,dc=com -h ldap.example.com '(&(cn=admins)(memberUid=testuser))'

If no results are returned, then the user is **not** authorized. If a
result is resturned, then the user **is** authorized.


AUTHORIZATION: ENVIRONMENT_CALLABLE
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The second authorization callable keys off ``AUTHORIZATION_ENVIRONMENT_CALLABLE`` configuration parameter.
This callable is responsible for deciding if a user has access to the environment(s).


rerest.authorization.envrestrictions:environment_allow_all
``````````````````````````````````````````````````````````
.. warning::
   This should not be used in a production environment

To use this callable set ``AUTHORIZATION_ENVIRONMENT_CALLABLE`` to ``rerest.authorization.envrestrictions:environment_allow_all``.


rerest.authorization.envrestrictions:environment_flat_files
```````````````````````````````````````````````````````````

To use this callable set ``AUTHORIZATION_CALLABLE`` to ``rerest.authorization.envrestrictions:environment_flat_files`` and set the following items
in your configuration file.

======================= ====== ====================== ================================================
Name                    Type   Parent                 Value
======================= ====== ====================== ================================================
ENVIRONMENT_FLAT_FILES  dict   None                   Dictionary holding mapping informationa. key/val is environment name: path to file
======================= ====== ====================== ================================================

Here is an example of what the *secion* would look like:

.. code-block:: json
   :linenos:

    {
        "AUTHORIZATION_ENVIRONMENT_CALLABLE": "rerest.authorization.envrestrictions:environment_flat_files",
        "ENVIRONMENT_FLAT_FILES": {
            "somegroup": ["dev", "qa"],
            "superadmins": ["dev", "qa", "stage", "production"]
        }
    }

.. _rerest_deployment:

RE-REST Deployment
~~~~~~~~~~~~~~~~~~


Apache with mod_wsgi
````````````````````
mod_wsgi can be used with Apache to mount rerest. Example mod_wsgi files are located in contrib/mod_wsgi.

* rerest.conf: The mod_wsgi configuration file. This should be modified and placed in /etc/httpd/conf.d/.
* rerest.wsgi: The WSGI file that mod_wsgi will use. This should be modified and placed in the location noted in rerest.conf

Gunicorn
````````
Gunicorn (http://gunicorn.org/) is a popular open source Python WSGI server. It's still recommend to use Apache (or another web server) to handle auth before gunicorn since gunicorn itself is not set up for it.

.. code-block:: bash

   $ gunicorn --user=YOUR_WORKER_USER --group=YOUR_WORKER_GROUP -D -b 127.0.0.1:5000 --access-logfile=/your/access.log --error-logfile=/your/error.log -e REREST_CONFIG=/full/path/to/settings.json rerest.app:app


Running From Source
~~~~~~~~~~~~~~~~~~~
To run directly from source in order to test out the server run:

.. code-block:: bash

   $ python rundevserver.py

The dev server will allow any HTTP Basic Auth user/password combination.


URLs
~~~~

/api/v0/*$GROUP*/playbook/*$PLAYBOOKID*/deployment/
`````````````````````````````````````````````````````

* **PUT**: Creates a new deployment.

 * **Response Type**: json
 * **Response Example**: ``{"status": "created", "id": 1}``
 * **Input Format**: None
 * **Inputs**: optional json

/api/v0/playbooks/
``````````````````
* **GET**: Gets a list of **all** playbooks.

 * **Response Type**: json
 * **Response Example**: ``{"status": "ok", "items": [...]}``
 * **Input Format**: None
 * **Inputs**: None


/api/v0/*$GROUP*/playbook/
````````````````````````````
* **GET**: Gets a list of all playbooks for a group.

 * **Response Type**: json
 * **Response Example**: ``{"status": "ok", "items": [...]}``
 * **Input Format**: None
 * **Inputs**: None

* **PUT**: Creates a new playbook.

 * **Response Type**: json
 * **Response Example**: ``{"status": "created", "id": "53614ccf1370129d6f29c7dd"}``
 * **Input Format**: json/yaml
 * **Inputs**: Optional format parameter which controls submit type. Can be json or yaml. Default is json.


/api/v0/*$GROUP*/playbook/*$ID*/
``````````````````````````````````
* **GET**: Gets a playbooks for a group.

 * **Response Type**: json/yaml
 * **Response Example**: ``{"status": "ok", "item": ...}``
 * **Input Format**: None
 * **Inputs**: Optional format parameter which controls response type. Can be json or yaml. Default is json.

* **POST**: Replace a playbook in a group.

 * **Response Type**: json
 * **Response Example**: ``{"status": "ok", "id": "53614ccf1370129d6f29c7dd"}``
 * **Input Format**: json/yaml
 * **Inputs**: Optional format parameter which controls response type. Can be json or yaml. Default is json.

* **DELETE**: Delete a playbook in a group.

 * **Response Type**: json
 * **Response Example**: ``{"status": "gone"}``
 * **Input Format**: None
 * **Inputs**: None



Platform Gotchas
~~~~~~~~~~~~~~~~~

RHEL 6
``````
You may need to add the following to your PYTHONPATH to be able to use Jinja2:

::

   /usr/lib/python2.6/site-packages/Jinja2-2.6-py2.6.egg


What's Happening
~~~~~~~~~~~~~~~~
#. User requests a new job via the REST endpoint
#. The REST server creates a temporary response queue and binds it to the exchange with the same name.
#. The REST server creates a message with a reply_to of the temporary response queue's topic.
#. The REST server sends the message to the bus on exchange *re* and topic *job.create*. Body Example: {"group": "nameofgroup"}
#. The REST server waits on the temporary response queue for a response.
#. Once a response is returned the REST service loads the body into a json structure and pulls out the id parameter.
#. The REST service then responds to the user with the job id.
#. The temporary response queue then is automatically deleted by the bus.


Usage Example
~~~~~~~~~~~~~
The authentication mechanism used in the front end webserver could be set up to use vastly different schemes. Instead of covering every possible authentication style which could be used we will work with two common ones in usage examples: htacces and kerberos.

.. note::
   Setting up the front end proxy server for authentication is out of scope for this documentation.

htaccess / HTTP Basic Auth
``````````````````````````
.. code-block:: bash

   $ curl -X PUT --user "USERNAME" -H "Content-Type: application/json" --data @file.json https://rerest.example.com/api/v0/test/playbook/
   Password:

   ... # 201 and json data if exists, otherwise an error code


kerberos
````````
.. code-block:: bash

   $ kinit -f USERNAME
   Password for USERNAME@DOMAIN:
   $ curl -u 'a:a' -H "Content-Type: application/json" --data @file.json -X PUT https://rerest.example.com/api/v0/test/playbook/

   ... # 201 and json data if exists, otherwise an error code


Dynamic Variables
`````````````````
.. _rerest_dynamic_variables:

Passing dynamic variables requires two additions

#. We must set the ``Content-Type`` header (``-H ...`` below) to ``application/json``
#. We must pass **data** (``-d '{....}'`` below) for the ``PUT`` to send to the server

This example sets the ``Content-Type`` and passes two **dynamic
variables**: ``cart`` which is the name of a `Juicer
<https://github.com/juicer/juicer>`_ release cart, and
``environment``, which is the environment to push the release cart
contents to.

.. code-block:: bash

  $ curl -u "user:passwd" -H "Content-Type: application/json" -d '{"cart": "bitmath", "environment": "re"}' -X PUT http://rerest.example.com/api/v0/test/playbook/12345/deployment/

   ... # 201 and json data if exists, otherwise an error code

.. seealso::

      * :ref:`RE-WORKER-JUICER <re_worker_juicer>`

      * :ref:`Playbooks → Dynamic Variables <playbooks_steps_dynamic>`
