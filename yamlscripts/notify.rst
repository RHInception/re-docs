.. _notify:

Notify
^^^^^^

* **Required**: No
* **Argument type**: ``dict``
* **Default**: None

The ``notify`` element allows you to set custom notification hooks
which trigger at different **phases** of each sequence step. For
example, you may desire to receive an email every time an RPM
promotion step completes, `or fails`.

Here's an example of a notify step that updates IRC when the step
has started:

.. code-block:: yaml
   :linenos:
   :emphasize-lines: 6,7,8

   ---
   # ...
       steps:
         - service:Restart:
             service: httpd
             notify:
               started:
                 irc: [ "PHB", "#teamchannel" ]

Line **6**
   Shows the beginning of the ``notify`` syntax.

Line **7**
   This indicates which **phase** of worker execution this notification is for.

   Recognized **phases** include: ``started``, ``completed``, and ``failed``

Line **8**
   Define the IRC notification parameters.

   For the ``irc`` item we define a list of users/channels for
   messages to be sent to.

   In this example, the user **PHB** would be notified `directly` with
   status updates. Additionally, a notification would be sent to the
   channel **#teamchannel**.

.. seealso::

   Components
      For a list of available notification workers, see the
      :ref:`Components <components_main>` section.
