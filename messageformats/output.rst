Output Message Format
----------------------

.. _output_message_format:

Notifications are sent out by workers connected to the Release Engine and follow a standard and very simple message format. This format is then consumed by the output worker who turns the standard format into messages in a file.

The Output Message Format has 1 key: message. The only other bit of information needed by an output worker is the correlation id which happens to be stored in the AMQP properties.

**Required Keys**:

* message: The message which should be written to a file.


**Example**:

.. code-block:: json

   {
       "message": "Something happened and you should know about it"
   }
