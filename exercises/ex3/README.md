# Exercise 3 – Persistent and Non-Persistent Quality of Service

[← Exercise 2](../ex2/README.md) | [Back to Overview](../../README.md) | [Next Exercise →](../ex4/README.md)

---
![Header](images/ex3-0.png)
---

- [Exercise 3.1 - Learn about delivery modes, persistency and quality of service](exercise-31-learn-about-delivery-modes-persistency-and-quality-of-service)
- [Exercise 3.2 - Experimenting with Persistency](exercise/ex3#experimenting-with-persistency)

---

After completing these steps you will have learned about delivery modes, persistency and quality of service.

## Exercise 3.1 - Learn about delivery modes, persistency and quality of service

Advanced Event Mesh supports multiple delivery modes for your events and messages. In this section we will explore what they are and how to use them.

### Persistence

Persistence refers to the **Quality-of-Service (QoS)** of event messages, which can be classified as **Persistent (Guaranteed)** or **Non-Persistent**.

**Persistent**: Event Messages are considered persistent if they are placed onto non-volatile storage media after they arrive on the broker. The broker's Data Plane stores a message if the message arrives with the `Persistent Delivery Mode` set to `Persistent`, or if a queue or durable topic endpoint has subscribed to a topic where messages are persisted regardless of the `Persistent Delivery` Mode flag setting in the message.

**Non-Persistent**: If an event message is sent with `Persistent Delivery Mode` set to `Direct`, which is synonymous with `Non-Persistent`, the message is only placed onto the non-volatile storage media if the message is subscribed by a Queue or a Durable Topic Endpoint. 

Messages flagged as `Persistent` result in an acknowledgment message being sent back to the producer after the message is stored.

 In other words, AEM broker distinguish between the sender's persistency mode controlled by the flag on the message and the subscriber's persistency mode controlled by the existence or non-existence of a queue or topic-endpoint.

 If a publisher wants to make sure that they can detect (and react to) any messages being lost on the network, they should set the `Persistent Delivery Mode` to `Persistent` on the message they are publishing. This will cause the **broker to acknowledge event messages it has successfully received** (and processed) or sent a negative acknowledgment if anything went wrong.

 If a subscriber wants to make sure they do not loose any event messages, then they should always set up a queue (or topic endpoint) and add their subscription on that endpoint. We refer to this as **topic to queue mapping**. This will ensure that all event messages matching the subscriptions will be stored in the persistent endpoint on the broker and delivered to the consumer in a **lossless/guaranteed way**. Consumers are then expected to acknowledge the successful receipt of each message, after which (and only under that circumstance) the broker will remove the event message from the queue/endpoint.

 **Persistent message delivery** is also referred to by other names depending on the protocol or context, here are some synonyms and similar terms:
 - `Guaranteed Messaging`, `QoS1` (and `QoS2`), `at-least-once delivery` (and sometimes `exactly-once delivery`)

 **Non-Persistent message delivery** is also referred to by other names depending on the protocol or context, here are some synonyms and similar terms:
 - `Direct Messaging`, `QoS0`, `at-most-once delivery`

## 3.2 - Experimenting with Persistency

In this section we are going to play a bit around with persistency to see what this really means in a practical example using built-in **Try-Me tool**.
So, let's open up the `Try-Me` tool used in the last exercise.

1. Remove all subscriptions from your subscriber and clear the messages
![Publish 1](images/ex3-1.png)

2. Enter the topic into the topic field for the publisher. Use a `<unique_topic>` and replace **\<unique_topic\>** with something like your initials or a nickname. And publish one message as direct.
![Publish 2](images/ex3-2.png)

> Did you receive anything?<br>
No, of course not. Our Subscriber is not subscribed to the topic. Let's go ahead and add the subscription.

3. Enter the topic into the topic field for the subscriber. Use a `<unique_topic>` and replace **\<unique_topic\>** with something like your initials or a nickname. And hit `Subscribe`.
![Subscriber subscribed](images/ex3-3.png)

> Did you receive anything now?<br>
No, still not. Our subscriber is using direct/non-persistent mode to subscribe directly to the topic on the broker. There's no persistency endpoint configured, so we don't receive any messages that were published while we were not subscribed or connected.<br>
Let's go ahead and publish another message.

4. Hit publish one more time.
![AEM Direct Message Received](images/ex3-4.png)

> Now we've received a message, because both our publisher and subscriber are connected at the same time! This is non-persistent messaging, sometimes also referred to as best-effort messaging. **Publishers and Subscribers need to be connected at the same time and reachable** so that the messages can be delivered immediately from memory (meaning no network issues). This is the fastest mode, but it's not guaranteed to be lossless.

5. Publish a couple more messages, then clear your subscribers' messages and remove the topic subscription.
![Subscriber clear](images/ex3-5.png)

13. Click on `Bind to an endpoint to receive guaranteed messages`, then enter the unique queue name you provided in [Exercise 1.2 - step#6](exercise-12---create-a-queue-in-advanced-event-mesh).
![Bind to endpoint](images/ex3-6.png)

14. Click on `Start Consume`.
![Consume from queue](images/ex3-7.png)

> What happened?<br>

Did you receive multiple messages just now?

How is that possible?

Well, the answer lies in the **persistent delivery mode** aka guaranteed messaging and **the queue we set up earlier today**. We also added a subscription to this queue to our topic.

Now while we were happily experimenting with the `Try-Me` tab here publishing to our topic and seeing if we receive any messages, all those messages were simultaneously attracted to our queue by our previously set up subscription and stored there for later consumption.

Now when we started our Queue consumer on our Try-Me tab just now, we just **received all those stored messages** that were published between when we set up the queue and assigned the subscription and now.

This is persistent messaging, publishers and consumers don't need to be online or processing messages at the same speed**. The broker will securely store all messages matching the queue's subscription and deliver it securely to the consumer when it comes online or is ready to process messages.

## Summary

You've now explored different delivery modes on the consumer side and the effect they have on message delivery and message loss.

[← Exercise 2](../ex2/README.md) | [Back to Overview](../../README.md) | [Next Exercise →](../ex4/README.md)