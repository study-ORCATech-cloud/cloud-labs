# LAB12: Building Event-Driven Applications with SQS, SNS, and EventBridge (Console & CLI)

## ⚡ Lab Overview

In this advanced lab, you'll implement **event-driven architecture** using AWS messaging and event services. You'll create a system that handles asynchronous communication between application components using **Amazon SQS** for reliable message queuing, **Amazon SNS** for pub/sub notifications, and **Amazon EventBridge** for event routing. These services are fundamental building blocks for creating loosely coupled, scalable, resilient applications in the cloud.

### 💼 Real-World DevOps Applications

Event-driven architecture is a cornerstone of modern cloud applications, offering several key advantages:

- **Decoupling**: Components can operate independently without direct dependencies
- **Scalability**: Services can scale individually based on their specific load
- **Resilience**: Failures in one component don't bring down the entire system
- **Asynchronous Processing**: Long-running tasks don't block user interactions
- **Event Sourcing**: Enables auditing, replay, and state reconstruction from event history

Common production use cases include:

- **Microservices Communication**: Reliable messaging between distributed services
- **Workflow Orchestration**: Coordinating multi-step business processes
- **Notifications**: Delivering alerts and updates across multiple channels
- **Data Pipelines**: Processing data streams and triggering downstream actions
- **System Integration**: Connecting disparate systems with different technologies

---

## 🎯 Objectives

By the end of this lab, you will be able to:
- Create and configure SQS queues for reliable message delivery
- Implement SNS topics with multiple subscription types
- Set up EventBridge event buses with custom rules and targets
- Connect these services to create comprehensive event-driven workflows
- Test message flow through the complete architecture
- Apply best practices for event-driven design in AWS

---

## 🛠️ Prerequisites

- AWS account with administrative permissions
- Basic understanding of messaging and event concepts
- Familiarity with JSON for event patterns and message formatting
- AWS CLI installed and configured (refer to [CLI Setup Guide](../../CLI-Setup.md))
- Access to an email account for testing SNS email notifications

---

# 🌐 PART 1: AWS MANAGEMENT CONSOLE IMPLEMENTATION

In this first part of the lab, you'll create and configure messaging and event services using the AWS Management Console.

## 📋 Step 1: Create SQS Queues (Console)

### Create a Standard Queue

1. Sign in to the AWS Management Console
2. Navigate to the **Amazon SQS** service
3. Click **Create queue**
4. For the **Type**, select **Standard**
5. For **Name**, enter `lab12-standard-queue`
6. Under **Configuration**:
   - **Visibility timeout**: `30 seconds`
   - **Message retention period**: `4 days`
   - **Maximum message size**: `256 KB`
   - **Delivery delay**: `0 seconds`
   - **Receive message wait time**: `0 seconds`
7. Under **Access policy**, keep the default (Only the queue owner can send and receive messages)
8. Click **Create queue**

### Create a Dead-Letter Queue

1. Click **Create queue** again
2. For the **Type**, select **Standard**
3. For **Name**, enter `lab12-dlq`
4. Keep all default settings
5. Click **Create queue**

### Configure Dead-Letter Queue for the Standard Queue

1. Return to the list of queues and select `lab12-standard-queue`
2. Click **Edit**
3. Scroll down to **Dead-letter queue**
4. Select **Enable** 
5. Under **Dead-letter queue destination**, select `lab12-dlq`
6. Set **Maximum receives** to `3`
7. Click **Save**

### Create a FIFO Queue

1. Click **Create queue**
2. For the **Type**, select **FIFO (First-In-First-Out)**
3. For **Name**, enter `lab12-fifo-queue.fifo` (note the .fifo suffix, which is required)
4. Under **Configuration**:
   - **Visibility timeout**: `30 seconds`
   - **Message retention period**: `4 days`
   - **Maximum message size**: `256 KB`
   - **Delivery delay**: `0 seconds`
   - **Receive message wait time**: `0 seconds`
   - **Content-based deduplication**: Enable this option
5. Keep all other settings as default
6. Click **Create queue**

## 📢 Step 2: Create an SNS Topic (Console)

### Create a Standard Topic

1. Navigate to the **Amazon SNS** service
2. In the navigation panel, choose **Topics**
3. Click **Create topic**
4. For **Type**, select **Standard**
5. For **Name**, enter `lab12-notifications`
6. Under **Access policy**, keep the default (Basic)
7. Leave all other settings as default
8. Click **Create topic**

### Create Email Subscription

1. Once the topic is created, you'll be on the topic details page
2. In the **Subscriptions** tab, click **Create subscription**
3. For **Protocol**, select **Email**
4. For **Endpoint**, enter your email address
5. Leave all other settings as default
6. Click **Create subscription**
7. Check your email inbox for a confirmation message from AWS
8. Open the email and click the **Confirm subscription** link

### Create SQS Subscription

1. In the **Subscriptions** tab, click **Create subscription** again
2. For **Protocol**, select **Amazon SQS**
3. For **Endpoint**, select the ARN of your `lab12-standard-queue` 
4. Leave all other settings as default
5. Click **Create subscription**

## 🔄 Step 3: Configure EventBridge (Console)

### Create a Custom Event Bus

1. Navigate to the **Amazon EventBridge** service
2. In the navigation panel, select **Event buses**
3. Click **Create event bus**
4. For **Name**, enter `lab12-event-bus`
5. Leave the **Event archive** and **Schema discovery** settings as default
6. Click **Create**

### Create an Event Rule

1. In the navigation panel, select **Rules**
2. Select your `lab12-event-bus` from the dropdown
3. Click **Create rule**
4. For **Name**, enter `lab12-notification-rule`
5. For **Event pattern**, select **Custom pattern**
6. Enter the following JSON pattern:
```json
{
  "source": ["lab12.events"],
  "detail-type": ["notification"],
  "detail": {
    "importance": ["high"]
  }
}
```
7. Click **Next**
8. For **Target types**, select **AWS service**
9. For **Select a target**, choose **SNS topic**
10. For **Topic**, select your `lab12-notifications` topic
11. Leave the rest as default and click **Next**
12. Skip the tags screen by clicking **Next**
13. Review your rule and click **Create rule**

### Create a Scheduled Rule

1. In the navigation panel, select **Rules** again
2. Ensure you have the `lab12-event-bus` selected
3. Click **Create rule**
4. For **Name**, enter `lab12-scheduled-rule`
5. Select **Schedule pattern**
6. Select **A schedule that runs at a regular rate, such as every 10 minutes**
7. For **Rate expression**, enter `5 minutes`
8. Click **Next**
9. For **Target types**, select **AWS service**
10. For **Select a target**, choose **SQS queue**
11. For **Queue**, select your `lab12-standard-queue`
12. For **Message body**, enter:
```json
{
  "message": "This is a scheduled event",
  "timestamp": "timestamp()"
}
```
13. Click **Next**
14. Skip the tags screen by clicking **Next**
15. Review your rule and click **Create rule**

## 🧩 Step 4: Test the Integration (Console)

### Send a Message to an SQS Queue

1. Navigate back to the **Amazon SQS** service
2. Select your `lab12-standard-queue`
3. Click **Send and receive messages**
4. In the **Message body**, enter:
```json
{
  "message": "This is a test message to SQS",
  "timestamp": "2023-09-01T12:00:00Z"
}
```
5. Click **Send message**
6. Scroll down to the **Receive messages** section
7. Click **Poll for messages**
8. You should see your message appear - select it to view the content
9. After viewing, click **Delete** to remove it from the queue

### Send a Message to an SNS Topic

1. Navigate to the **Amazon SNS** service
2. Select your `lab12-notifications` topic
3. Click **Publish message**
4. For **Subject**, enter `Test Notification`
5. For **Message body**, enter:
```json
{
  "message": "This is a test notification from SNS",
  "timestamp": "2023-09-01T12:00:00Z"
}
```
6. Scroll down and click **Publish message**
7. Check your email for the received notification
8. Navigate back to the **Amazon SQS** service
9. Select your `lab12-standard-queue` and click **Send and receive messages**
10. Click **Poll for messages**
11. You should see the message that was sent to your SNS topic and forwarded to SQS

### Send an Event to EventBridge

1. Navigate to the **Amazon EventBridge** service
2. In the left navigation panel, select **Event buses**
3. Select your `lab12-event-bus`
4. Click **Send events**
5. For **Event source**, enter `lab12.events`
6. For **Detail type**, enter `notification`
7. For **Event detail**, enter:
```json
{
  "message": "This is a high priority notification",
  "importance": "high",
  "timestamp": "2023-09-01T12:00:00Z"
}
```
8. Click **Send**
9. Check your email for the notification (delivered through SNS)
10. Check your `lab12-standard-queue` for the same message (delivered through SNS to SQS)

## 🧹 Step 5: Console Implementation Cleanup

To avoid ongoing charges, clean up the resources created in the console part of this lab:

1. **Delete EventBridge Rules**:
   - Navigate to **Amazon EventBridge**
   - Select **Rules**
   - Select and delete `lab12-notification-rule` and `lab12-scheduled-rule`

2. **Delete EventBridge Event Bus**:
   - Navigate to **Event buses**
   - Select `lab12-event-bus`
   - Click **Delete**
   - Confirm by typing the name and click **Delete**

3. **Delete SNS Subscriptions**:
   - Navigate to **Amazon SNS**
   - Click **Subscriptions**
   - Select and delete all subscriptions created for the `lab12-notifications` topic

4. **Delete SNS Topic**:
   - Click **Topics**
   - Select `lab12-notifications`
   - Click **Delete**
   - Type `delete me` and click **Delete**

5. **Delete SQS Queues**:
   - Navigate to **Amazon SQS**
   - Select all queues created for this lab
   - Click **Delete**
   - Type `delete` to confirm and click **Delete**

---

# 💻 PART 2: AWS CLI IMPLEMENTATION

In this second part of the lab, you'll create the same messaging and event resources using the AWS Command Line Interface (CLI).

## 📋 Step 1: Create SQS Queues (CLI)

```bash
# Create a standard queue
aws sqs create-queue \
  --queue-name lab12-standard-queue \
  --attributes "{\"VisibilityTimeout\":\"30\", \"MessageRetentionPeriod\":\"345600\"}" \
  --region eu-west-1

# Store the queue URL and ARN for later use
STANDARD_QUEUE_URL=$(aws sqs get-queue-url --queue-name lab12-standard-queue --region eu-west-1 --query 'QueueUrl' --output text)
STANDARD_QUEUE_ARN=$(aws sqs get-queue-attributes --queue-url $STANDARD_QUEUE_URL --attribute-names QueueArn --region eu-west-1 --query 'Attributes.QueueArn' --output text)

echo "Standard Queue URL: $STANDARD_QUEUE_URL"
echo "Standard Queue ARN: $STANDARD_QUEUE_ARN"

# Create a dead-letter queue
aws sqs create-queue \
  --queue-name lab12-dlq \
  --region eu-west-1

# Store the DLQ URL and ARN
DLQ_URL=$(aws sqs get-queue-url --queue-name lab12-dlq --region eu-west-1 --query 'QueueUrl' --output text)
DLQ_ARN=$(aws sqs get-queue-attributes --queue-url $DLQ_URL --attribute-names QueueArn --region eu-west-1 --query 'Attributes.QueueArn' --output text)

echo "DLQ URL: $DLQ_URL"
echo "DLQ ARN: $DLQ_ARN"

# Configure the standard queue to use the DLQ
aws sqs set-queue-attributes \
  --queue-url $STANDARD_QUEUE_URL \
  --attributes "{\"RedrivePolicy\":\"{\\\"deadLetterTargetArn\\\":\\\"$DLQ_ARN\\\",\\\"maxReceiveCount\\\":\\\"3\\\"}\"}" \
  --region eu-west-1

# Create a FIFO queue
aws sqs create-queue \
  --queue-name lab12-fifo-queue.fifo \
  --attributes "{\"FifoQueue\":\"true\", \"ContentBasedDeduplication\":\"true\"}" \
  --region eu-west-1

# Store the FIFO queue URL and ARN
FIFO_QUEUE_URL=$(aws sqs get-queue-url --queue-name lab12-fifo-queue.fifo --region eu-west-1 --query 'QueueUrl' --output text)
FIFO_QUEUE_ARN=$(aws sqs get-queue-attributes --queue-url $FIFO_QUEUE_URL --attribute-names QueueArn --region eu-west-1 --query 'Attributes.QueueArn' --output text)

echo "FIFO Queue URL: $FIFO_QUEUE_URL"
echo "FIFO Queue ARN: $FIFO_QUEUE_ARN"
```

## 📢 Step 2: Create an SNS Topic (CLI)

```bash
# Create a standard SNS topic
aws sns create-topic \
  --name lab12-notifications \
  --region eu-west-1

# Store the topic ARN
TOPIC_ARN=$(aws sns list-topics --region eu-west-1 --query 'Topics[?ends_with(TopicArn, `:lab12-notifications`)].[TopicArn]' --output text)
echo "Topic ARN: $TOPIC_ARN"

# Create an email subscription (replace with your email)
aws sns subscribe \
  --topic-arn $TOPIC_ARN \
  --protocol email \
  --notification-endpoint your.email@example.com \
  --region eu-west-1

echo "Check your email and confirm the subscription"

# Create an SQS subscription
aws sns subscribe \
  --topic-arn $TOPIC_ARN \
  --protocol sqs \
  --notification-endpoint $STANDARD_QUEUE_ARN \
  --region eu-west-1

# Store the SQS subscription ARN
SQS_SUBSCRIPTION_ARN=$(aws sns list-subscriptions-by-topic --topic-arn $TOPIC_ARN --region eu-west-1 --query 'Subscriptions[?Endpoint==`'$STANDARD_QUEUE_ARN'`].SubscriptionArn' --output text)
echo "SQS Subscription ARN: $SQS_SUBSCRIPTION_ARN"

# Update the SQS queue policy to allow SNS to send messages
cat > queue-policy.json << EOL
{
  "Version": "2012-10-17",
  "Id": "${STANDARD_QUEUE_ARN}/SQSDefaultPolicy",
  "Statement": [
    {
      "Sid": "Allow-SNS-SendMessage",
      "Effect": "Allow",
      "Principal": {
        "Service": "sns.amazonaws.com"
      },
      "Action": "sqs:SendMessage",
      "Resource": "${STANDARD_QUEUE_ARN}",
      "Condition": {
        "ArnEquals": {
          "aws:SourceArn": "${TOPIC_ARN}"
        }
      }
    }
  ]
}
EOL

aws sqs set-queue-attributes \
  --queue-url $STANDARD_QUEUE_URL \
  --attributes '{"Policy": '$(cat queue-policy.json | jq -c .)'} ' \
  --region eu-west-1
```

## 🔄 Step 3: Configure EventBridge (CLI)

```bash
# Create a custom event bus
aws events create-event-bus \
  --name lab12-event-bus \
  --region eu-west-1

# Store the event bus ARN
EVENT_BUS_ARN=$(aws events describe-event-bus --name lab12-event-bus --region eu-west-1 --query 'Arn' --output text)
echo "Event Bus ARN: $EVENT_BUS_ARN"

# Create a rule for notifications
cat > notification-rule-pattern.json << EOL
{
  "source": ["lab12.events"],
  "detail-type": ["notification"],
  "detail": {
    "importance": ["high"]
  }
}
EOL

aws events put-rule \
  --name lab12-notification-rule \
  --event-pattern file://notification-rule-pattern.json \
  --event-bus-name lab12-event-bus \
  --region eu-west-1

# Store the rule ARN
RULE_ARN=$(aws events describe-rule --name lab12-notification-rule --event-bus-name lab12-event-bus --region eu-west-1 --query 'Arn' --output text)
echo "Rule ARN: $RULE_ARN"

# Add SNS topic as a target for the rule
aws events put-targets \
  --rule lab12-notification-rule \
  --event-bus-name lab12-event-bus \
  --targets '[{"Id": "1", "Arn": "'$TOPIC_ARN'"}]' \
  --region eu-west-1

# Create a scheduled rule
aws events put-rule \
  --name lab12-scheduled-rule \
  --schedule-expression "rate(5 minutes)" \
  --event-bus-name lab12-event-bus \
  --region eu-west-1

# Store the scheduled rule ARN
SCHEDULED_RULE_ARN=$(aws events describe-rule --name lab12-scheduled-rule --event-bus-name lab12-event-bus --region eu-west-1 --query 'Arn' --output text)
echo "Scheduled Rule ARN: $SCHEDULED_RULE_ARN"

# Create input payload for the scheduled event
cat > scheduled-input.json << EOL
{
  "message": "This is a scheduled event",
  "timestamp": "timestamp()"
}
EOL

# Add SQS queue as a target for the scheduled rule
aws events put-targets \
  --rule lab12-scheduled-rule \
  --event-bus-name lab12-event-bus \
  --targets '[{"Id": "2", "Arn": "'$STANDARD_QUEUE_ARN'", "Input": '$(cat scheduled-input.json | jq -c .)'}]' \
  --region eu-west-1
```

## 🧩 Step 4: Test the Integration (CLI)

```bash
# Send a message to SQS
aws sqs send-message \
  --queue-url $STANDARD_QUEUE_URL \
  --message-body '{"message": "This is a test message to SQS", "timestamp": "2023-09-01T12:00:00Z"}' \
  --region eu-west-1

# Receive the message from SQS
aws sqs receive-message \
  --queue-url $STANDARD_QUEUE_URL \
  --max-number-of-messages 10 \
  --wait-time-seconds 10 \
  --region eu-west-1

# Send a message to the FIFO queue
aws sqs send-message \
  --queue-url $FIFO_QUEUE_URL \
  --message-body '{"message": "This is a test message to FIFO", "timestamp": "2023-09-01T12:00:00Z"}' \
  --message-group-id test-group \
  --region eu-west-1

# Publish a message to SNS topic
aws sns publish \
  --topic-arn $TOPIC_ARN \
  --subject "Test Notification" \
  --message '{"message": "This is a test notification from SNS", "timestamp": "2023-09-01T12:00:00Z"}' \
  --region eu-west-1

# Send an event to EventBridge
aws events put-events \
  --entries '[{
    "Source": "lab12.events",
    "DetailType": "notification",
    "Detail": "{\"message\": \"This is a high priority notification\", \"importance\": \"high\", \"timestamp\": \"2023-09-01T12:00:00Z\"}",
    "EventBusName": "lab12-event-bus"
  }]' \
  --region eu-west-1

# Check SQS for messages (after allowing time for delivery)
echo "Waiting 10 seconds for messages to be delivered..."
sleep 10
aws sqs receive-message \
  --queue-url $STANDARD_QUEUE_URL \
  --max-number-of-messages 10 \
  --wait-time-seconds 10 \
  --region eu-west-1
```

## 🧹 Step 5: CLI Implementation Cleanup

To avoid ongoing charges, clean up the resources created in the CLI part of this lab:

```bash
# Remove targets from EventBridge rules
aws events remove-targets \
  --rule lab12-notification-rule \
  --event-bus-name lab12-event-bus \
  --ids "1" \
  --region eu-west-1

aws events remove-targets \
  --rule lab12-scheduled-rule \
  --event-bus-name lab12-event-bus \
  --ids "2" \
  --region eu-west-1

# Delete EventBridge rules
aws events delete-rule \
  --name lab12-notification-rule \
  --event-bus-name lab12-event-bus \
  --region eu-west-1

aws events delete-rule \
  --name lab12-scheduled-rule \
  --event-bus-name lab12-event-bus \
  --region eu-west-1

# Delete EventBridge event bus
aws events delete-event-bus \
  --name lab12-event-bus \
  --region eu-west-1

# List and delete all SNS subscriptions for the topic
SUBSCRIPTIONS=$(aws sns list-subscriptions-by-topic --topic-arn $TOPIC_ARN --region eu-west-1 --query 'Subscriptions[].SubscriptionArn' --output text)
for SUB in $SUBSCRIPTIONS; do
  if [[ $SUB != "PendingConfirmation" ]]; then
    aws sns unsubscribe --subscription-arn $SUB --region eu-west-1
    echo "Deleted subscription: $SUB"
  fi
done

# Delete SNS topic
aws sns delete-topic \
  --topic-arn $TOPIC_ARN \
  --region eu-west-1

# Delete SQS queues
aws sqs delete-queue \
  --queue-url $STANDARD_QUEUE_URL \
  --region eu-west-1

aws sqs delete-queue \
  --queue-url $FIFO_QUEUE_URL \
  --region eu-west-1

aws sqs delete-queue \
  --queue-url $DLQ_URL \
  --region eu-west-1

# Clean up temporary files
rm -f queue-policy.json notification-rule-pattern.json scheduled-input.json

echo "Cleanup complete"
```

---

## 🧪 Optional Challenges

To deepen your understanding of AWS messaging and event services, try these additional challenges:

1. **Message Filtering**: Implement an SNS subscription filter policy to only receive messages matching specific criteria
2. **SQS Message Delay**: Configure a delay queue that holds messages for a specified period before they become available
3. **EventBridge Schema**: Define an event schema and validate events against it
4. **Cross-Account Delivery**: Set up EventBridge rules to send events to targets in a different AWS account
5. **Message Transformation**: Use EventBridge input transformers to modify event data before delivering to targets

---

## 🎓 Summary

In this lab, you've learned how to:
- Create and configure SQS queues for reliable message delivery
- Set up SNS topics with multiple subscription types
- Build EventBridge event buses with custom rules and targets
- Integrate these services to create comprehensive event-driven workflows
- Test message flow through your architecture

These skills form the foundation for designing and implementing event-driven architectures in AWS. By decoupling your application components through messaging and events, you can build more resilient, scalable, and maintainable systems. 