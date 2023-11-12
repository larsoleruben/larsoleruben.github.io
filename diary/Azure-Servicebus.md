---
layout: page
title: AZ Service-Bus
permalink: /diary/az-bus
---
# Process Takes a Long Time and I Get a Lost Lock Exception

## Queue and Topic Setup
Often, there is a desire to treat (i.e., complete or deadletter) the received message after some process has finished. You may suddenly find that this prevents your service from performing as intended. Worse yet, everything slows down because the same message is treated repeatedly until it finally reaches its end of TTL (time to live). Only then is the next message processed. To exacerbate matters, all your other messages have probably also reached the TTL boundary and will be deadlettered (if so chosen).  
Some concepts here are a bit hard to grasp and are not very intuitive.

### Setting Up the Queue or Subscription
When setting up the queue/subscription, there are some very important distinctions to be aware of. Most of these are valid, whether it is a queue or a topic/subscription.
Here are the options and some information about them:

![bus setup](larsoleruben/larsoleruben.github.io/assets/bus-setup.png)

- The "Never Auto-Delete" should be checked if you want to use the bus for more than a few days.
- Until now, as far as I know, we have only one use-case in rawtoggold that needs session-enabled queues/subscriptions. Also, be aware that handling sessions is not straightforward, especially if you use more than one service running in parallel, connecting to the same queue/subscription.
- Set the time to live to something you think is reasonable, like 3 hours.
- Enable deadlettering on message expiration.
- Message Lock Duration: This is the tricky one. Despite the apparent possibilities, the maximum lock time is 5 minutes. The default is 1 minute, and you should probably stick to that and then renew the lock. See the explanation below.

### How Does the Lock Setup Actually Work?
Suppose you have set the lock length to one minute; the following scenarios are possible:

#### Scenario 1: Process Ends in Less Than One Minute
1. Your script reads the message and puts a lock on it => nobody else can read the message.
2. Your script tries to process the message or fails in less than one minute => your script can easily either complete the message or deadletter it.

#### Scenario 2: Process Lasts More Than One Minute
1. Your script reads the message and puts a lock on it => nobody else can read the message.
2. Your script tries to process the message or fails in more than one minute => your script tries to either complete the message or deadletter it, but it can't because the lock has expired, resulting in a `MessageLockLostError` => the message is now free for any other subscriber, and you can end up with the same message being processed (maybe correctly) over and over again.

#### Scenario 3: Process Lasts More Than One Minute, But Lock is Renewed
1. Your script reads the message and puts a lock on it => nobody else can read the message.
2. You attach an auto renewer to the message, with a max of, say, 15 minutes.
3. Your script tries to process the message or fails in more than one minute => your script retains the lock, even though the subscription is set up to only 1 minute in Azure. So you'll be able to complete or deadletter the message in a total of 15 minutes.

#### How Does This Look in Code?
Remember, there are two types of locks: A session lock and a message lock. Since our queue/subscription is not session-enabled, we will concentrate on the message lock. This is the way it's done in `obs_utils`. `_with_session` is false (this has to be refactored away as it does not work as intended):

```python
def _process_messages(self, receiver: ServiceBusReceiver):
    # NOTE: This is an important help
    renewer = AutoLockRenewer()
    if self._with_session:
        self._set_lock_on_receiver(receiver)
    self._set_graceful_shutdown(close_func=receiver.close)

    try:
        # NOTE: First we download a number of messages (only 1 here)
        messages = receiver.receive_messages(
            max_wait_time=5,
            max_message_count=1,
        )
        # NOTE: We iterate the downloaded messages and register them with the renewer
        # NOTE: max_lock_renewal_duration is like 2 hours, 
        # so our process has to be finished in 2 hours
        for message in messages:
            renewer.register(receiver, message, max_lock_renewal_duration=self._session_lock_duration_in_seconds)

        for message in messages:
            if remove_quote(str(message).strip()).lower() == self._raw_shutdown_message:
                self._handle_shutdown_message(message=message, receiver=receiver)
                break
            try:
                self._logger.debug(f"Message with session_id='{message.session_id}'")
                if self._with_session:
                    receiver.session.set_state(QueueSessionStates.OPENED.value)
                self._process_one_message(message, receiver=receiver)
            except ContinueException:
                continue
    # This should have been a MessageLockLostError and will catch it after 2 hours
    # As a whole, this exception must be refactored
    # TODO Refactor this
    except SessionLockLostError:
        self._logger.warning(f"Session with session_id {receiver.session} expired. Closing session")
        self._session_lock_renewer.close()
        receiver.close()
```
Some usefull links:
- Some Microsoft [examples in python](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/servicebus/azure-servicebus/samples)
- [What is an azure service bus](https://learn.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview)
- [Service Bus queues, topics, and subscriptions](https://learn.microsoft.com/en-us/azure/service-bus-messaging/service-bus-queues-topics-subscriptions)
