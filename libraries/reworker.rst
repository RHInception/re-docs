.. _re_worker:

RE-WORKER
---------
This library provides a simple base for release engine workers to build from.


.. _re_worker_implementation:

Implementing a Worker
~~~~~~~~~~~~~~~~~~~~~
To implement a worker subclass off of **reworker.worker.Worker** and override the **process** method.

If there are any inputs that need to be passed in the class level variable **dynamic** should be populated.

.. code-block:: python

   class MyWorker(Worker):

       dynamic = ('environment', 'cart')

       ...

If a **config_file** is passed in on Worker creation it will be loaded
as JSON and available as ``self._config``. Otherwise ``self._config``
will be an empty dictionary.


Worker also provides a few convenience methods to simplify use:

.. todo::
   Change to using actual sphinx API documentation.

Worker.send
```````````
Sends a message.

* **Inputs**:

 * topic: the routing key
 * corr_id: the correlation id
 * message_struct: the dict or list to send as the body
 * exchange: set to **''** to reply back to the FSM

* **Returns**: None

Worker.notify
`````````````
* **Inputs**:

 * slug: the short text to use in the notification
 * message: a string which will be used in the notification
 * phase: the phase to identify with in the notification
 * corr_id: the correlation id. Default: None
 * exchange: the exchange to publish on. Default: re

* **Returns**: None

Worker.ack
``````````
Acks a message.

* **Inputs**:

 * basic_deliver: pika.Spec.Basic.Deliver instance

* **Returns**: None

Worker.run_forever
``````````````````
Starts the main loop.

* **Inputs**: None
* **Returns**: None

Worker.process
``````````````
What a worker should do when a message is received. All output
should be written to the output logger.

* **Inputs**:

 * channel: pika.channel.Channel instance
 * basic_deliver: pika.Spec.Basic.Deliver instance
 * properties: pika.Spec.BasicProperties instance (ex: headers)
 * body: dict or list that was json loaded off the message
 * output: logger like instance to send output

* **Returns**: None


Running
~~~~~~~
To run an instance you will need to make an instance of your worker by passing in a few items.

* **Inputs**:

  * mq_config: should house: user, password, server, port and vhost.
  * config_file: is an optional full path to a json config file
  * logger: is an optional logger. Defaults to a logger to stderr


Example
~~~~~~~

.. code-block:: python

   from reworker.worker import Worker

   class IPrintStuff(Worker):

       def process(self, channel, basic_deliver, properties, body, output):
           print body  # This is a loaded json structure
           output.info(str(body))  # output is the logger for process output
           self.ack(basic_deliver) # ack at the end


   mq_conf = {
       'server': '127.0.0.1',
        'port': 5672,
        'vhost': '/',
        'user': 'guest',
        'password': 'guest',
   }

   worker = IPrintStuff(mq_conf)
   worker.run_forever()


To Turn this into a runnable script you'll rant to use ``reworker.worker.runner`` like so:


Example
~~~~~~~

.. code-block:: python

  from reworker.worker import Worker

  class IPrintStuff(Worker):
      ...


  def main():
      from reworker.worker import runner
      runner(IPrintStuff)


  if __name__ == '__main__':
      main()

For a more in-depth example see the `examples/ <https://github.com/RHInception/re-worker/tree/master/examples>`_ folder.
