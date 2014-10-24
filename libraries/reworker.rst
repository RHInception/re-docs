.. _re_worker:

RE-WORKER
---------
This library provides a simple base for release engine workers to build from.

.. admonition:: New in 0.0.5

   Workers using re-worker will attempt to reconnect if disconnected from the bus.

.. _re_worker_implementation:

Implementing a Worker
~~~~~~~~~~~~~~~~~~~~~
.. note:: To get going even **faster**, check out the `worker
          quickstart
          <https://github.com/RHInception/re-worker-quickstart/blob/master/README-QUICKSTART.md>`_
          repository!

To implement a worker, subclass off of ``reworker.worker.Worker`` and
override the ``process`` method.

If there are any inputs that need to be passed in, the class level
variable ``dynamic`` should be populated.

.. code-block:: python

   class MyWorker(Worker):

       dynamic = ('environment', 'cart')

       ...

If a ``config_file`` is passed in on Worker creation it will be loaded
as JSON and available as ``self._config``. Otherwise ``self._config``
will be an empty dictionary.


.. _re_worker_logging:

Logging
```````
When implementing your own worker, the ``re-worker`` base-class
provides two mechanisms for logging and reporting worker progress.

**Application-level**
   Recording the kind of information system administrators need to see
   for debugging is facilitated by the ``self.app_logger`` instance
   variable. This information is logged to ``stdout``.

.. code-block:: python
   :linenos:

   def process(self, channel, basic_deliver, properties, body, output):

       # ...

       self.app_logger.debug("Going to frob the widget.")

**User-level**
   Reporting progress is facilitated by the ``output`` parameter which
   is passed to the ``Worker.process`` method. This information is
   send back to the message bus where it is then collected and saved
   by the :ref:`output worker <re_worker_output>`.

.. code-block:: python
   :linenos:

   def process(self, channel, basic_deliver, properties, body, output):
       self.output = output

       # ...

       self.output.info("Release step successful with result: %s" % (
           str(result)))


Convenience Methods
~~~~~~~~~~~~~~~~~~~

**Worker** also provides a few convenience methods to simplify use:

.. todo::
   Change to using actual sphinx API documentation.


.. _reworker_send:

Worker.send
```````````
Sends a message.

* **Inputs**:

 * ``topic``: the routing key
 * ``corr_id``: the correlation id
 * ``message_struct``: the dict or list to send as the body
 * ``exchange``: set to **''** to reply back to the FSM

* **Returns**: None

Worker.notify
`````````````
* **Inputs**:

 * ``slug``: the short text to use in the notification
 * ``message``: a string which will be used in the notification
 * ``phase``: the phase to identify with in the notification
 * ``corr_id``: the correlation id. Default: **None**
 * ``exchange``: the exchange to publish on. Default: **re**

* **Returns**: None

Worker.ack
``````````
Acks a message.

* **Inputs**:

 * ``basic_deliver``: `pika.Spec.Basic.Deliver
   <http://pika.readthedocs.org/en/latest/modules/spec.html#pika.spec.Basic.Deliver>`_
   instance

* **Returns**: None

Worker._connect
```````````````
Internal connection method. This is also used when trying to reconnect.

.. versionadded:: 0.0.5

* **Inputs**: None
* **Returns**: None

Worker.run_forever
``````````````````
Starts the main loop.

* **Inputs**: None
* **Returns**: None

Worker.process
``````````````
What a worker should do when a message is received. All output
should be written to the :ref:`output logger <re_worker_logging>`.

* **Inputs**:

 * ``channel``: `pika.channel.Channel
   <http://pika.readthedocs.org/en/latest/modules/channel.html#pika.channel.Channel>`_
   instance
 * ``basic_deliver``: `pika.Spec.Basic.Deliver
   <http://pika.readthedocs.org/en/latest/modules/spec.html#pika.spec.Basic.Deliver>`_
   instance
 * ``properties``: `pika.Spec.BasicProperties
   <http://pika.readthedocs.org/en/latest/modules/spec.html#pika.spec.BasicProperties>`_
   instance (ex: headers)
 * ``body``: dict or list that was json loaded off the message
 * ``output``: logger like instance to send output

* **Returns**: None


Running
~~~~~~~
.. todo::
   Update this with how to run a custom **non-packaged** worker from source.

To run an instance you will need to make an instance of your worker by
passing in a few items.

* **Inputs**:

  * mq_config: should house: user, password, server, port and vhost.
  * config_file: is an optional full path to a json config file
  * logger: is an optional logger. Defaults to a logger to stderr
