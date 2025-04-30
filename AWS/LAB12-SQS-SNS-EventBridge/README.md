# AWS LAB12: Resource Overview – SQS, SNS, and EventBridge

## 📨 What Resources Are We Working With?

In **AWS LAB12**, you'll explore **event-driven architecture** using **Amazon SQS**, **Amazon SNS**, and **Amazon EventBridge**. This lab teaches you how to implement messaging, publish/subscribe notifications, and event routing patterns that enable loosely coupled, scalable microservices and serverless applications.

---

## 📋 Amazon Simple Queue Service (SQS)

### Description
SQS is a fully managed message queuing service that enables you to decouple and scale microservices, distributed systems, and serverless applications.

### Key Concepts
- **Queue**: Temporary storage for messages while they await processing
- **Standard Queue**: High throughput, at-least-once delivery, best-effort ordering
- **FIFO Queue**: First-in-first-out delivery, exactly-once processing
- **Message Lifecycle**: Sent → Retrieved → Processed → Deleted
- **Visibility Timeout**: Time a message is invisible after retrieval

### What You Do in the Lab
- Create standard and FIFO queues
- Send and receive messages
- Configure message retention and visibility settings
- Implement dead-letter queues

---

## 📢 Amazon Simple Notification Service (SNS)

### Description
SNS is a fully managed pub/sub messaging service for application-to-application (A2A) and application-to-person (A2P) communication.

### Key Concepts
- **Topic**: Communication channel for publishing messages
- **Subscription**: Endpoint that receives messages from a topic
- **Publish/Subscribe Model**: One-to-many message distribution
- **Protocol Types**: Lambda, SQS, HTTP/S, Email, SMS, Mobile push

### What You Do in the Lab
- Create a notification topic
- Add subscribers with different protocols
- Publish messages to subscribers
- Filter messages with subscription filter policies

---

## 🔄 Amazon EventBridge

### Description
EventBridge is a serverless event bus that connects application data from your own applications, SaaS solutions, and AWS services through events.

### Key Concepts
- **Event Bus**: Route that receives events
- **Rule**: Matches incoming events and routes them to targets
- **Target**: Resource that processes events
- **Event Pattern**: JSON pattern for matching events

### What You Do in the Lab
- Create custom event buses
- Configure event rules with event patterns
- Set up targets for event processing
- Schedule automated events

---

## 🔌 Service Integration

### Description
In this lab, you'll learn how these three services can work together to create comprehensive event-driven architectures.

### What You Do in the Lab
- Connect SNS topics to SQS queues
- Set up EventBridge rules to target SNS topics
- Create multi-step message processing workflows
- Implement fanout message distribution patterns

---

## ✅ Summary

| Resource                | Purpose                                             | Created Using    |
|-------------------------|-----------------------------------------------------|------------------|
| SQS Queues              | Message queuing for reliable asynchronous processing | Console & CLI    |
| SNS Topics              | Pub/sub messaging for notifications and fanout      | Console & CLI    |
| EventBridge Event Buses | Event routing for service and application events    | Console & CLI    |
| Event Rules             | Matching and processing events based on patterns    | Console & CLI    |
| Service Integrations    | Connecting services for comprehensive workflows     | Console & CLI    |

This lab provides hands-on experience with AWS's core messaging and event services, teaching you how to build resilient, decoupled, event-driven applications that can scale independently and communicate reliably. 