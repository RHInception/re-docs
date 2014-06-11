Notification Message Format
---------------------------

.. _notification_message_format:

Notifications are sent out by components of the Release Engine and follow a standard message format. This format is then consumed
by notification workers who turn the standard format into an external notification of some kind (like email).

The Notification Message Format has 4 required keys: slug, message, phase and target.

**Required Keys**:

* slug: A "short" message (up to 80 characters).
* message: The message of the notification.
* phase: The phase that the notification occured within: "started", "completed", "failed"
* target: A list denoting where the notification should go. This may be irc nicknames, email address, etc.. and is different for different workers.

.. note::
   Even though slug and message are required it does not mean the notification worker will use them both. Some notification workers
   will only use one or the other due to space constraints. However, if either key is missing the notification will be rejected as
   malformed and/or cause problems!

**Example**:

.. code-block:: json

   {
       "slug": "RPM's have been promoted",
       "message": "The rpms in deployment 12345667890 have been promoted from DEV to QA and are ready for installation.",
       "phase": "completed",
       "target": ["someone@example.com"]
   }
