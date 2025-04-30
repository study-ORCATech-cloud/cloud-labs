# LAB16: Orchestrating Workflows with AWS Step Functions State Machines

## ⚡ Lab Overview

In this advanced lab, you'll work with **AWS Step Functions**, a serverless orchestration service that allows you to coordinate multiple AWS services into structured, visual workflows. You'll learn how to create state machines that define the steps and logic of your workflows, integrate with Lambda functions and other AWS services, and implement error handling and retry logic. Step Functions helps you build resilient, maintainable applications by separating workflow logic from application code.

### 💼 Real-World DevOps Applications

Step Functions is a powerful service for automating complex processes and workflows, offering several key advantages:

- **Workflow Visualization**: Visual representation of application workflow logic
- **Reduced Complexity**: Coordinate multiple services without complex code
- **Error Handling**: Built-in retry mechanisms and error recovery
- **Execution History**: Detailed audit trail of workflow execution
- **State Management**: Automatic tracking of execution state and data
- **Service Integration**: Direct integration with AWS services

Common production use cases include:

- **Microservice Orchestration**: Coordinate multiple microservices in a workflow
- **Data Processing Pipelines**: Orchestrate ETL processes and data transformations
- **Transaction Processing**: Implement distributed transactions across services
- **IT Automation**: Automate infrastructure provisioning and management
- **Order Processing**: Manage complex order fulfillment processes
- **Approval Workflows**: Implement human approval steps in automated processes

---

## 🎯 Objectives

By the end of this lab, you will be able to:
- Create and configure AWS Step Functions state machines
- Design workflows using different state types (Task, Choice, Parallel, etc.)
- Implement Lambda functions and integrate them with Step Functions
- Configure error handling and retry logic for workflow resilience
- Monitor and debug state machine executions
- Deploy Step Functions workflows using both the console and AWS CLI

---

## 🛠️ Prerequisites

- AWS account with administrative permissions
- Basic understanding of serverless concepts
- Familiarity with AWS Lambda
- Basic knowledge of JSON for state machine definitions
- AWS CLI installed and configured (refer to [CLI Setup Guide](../../CLI-Setup.md))

---

# 🌐 PART 1: AWS MANAGEMENT CONSOLE IMPLEMENTATION

In this first part of the lab, you'll create and configure Step Functions resources using the AWS Management Console.

## 📊 Step 1: Create Lambda Functions for Workflow Steps

Let's create a few simple Lambda functions that will be used as tasks in our state machine:

### Create the First Lambda Function (ProcessOrder)

1. Sign in to the AWS Management Console
2. Navigate to the **Lambda** service
3. Click **Create function**
4. For **Function name**, enter `lab16-ProcessOrder`
5. For **Runtime**, select **Python 3.9**
6. Under **Permissions**, keep the default selection to create a new role with basic Lambda permissions
7. Click **Create function**
8. In the function code editor, replace the code with:

```python
def lambda_handler(event, context):
    # Extract order details from the input
    order_id = event.get('order_id', 'unknown')
    customer_id = event.get('customer_id', 'unknown')
    order_items = event.get('items', [])
    
    # Process the order (in a real scenario, this would do more)
    total_amount = sum(item.get('price', 0) * item.get('quantity', 0) for item in order_items)
    
    # Return the processed order information
    return {
        'order_id': order_id,
        'customer_id': customer_id,
        'items': order_items,
        'total_amount': total_amount,
        'status': 'processed',
        'order_processed': True,
        'message': f'Order {order_id} processed successfully'
    }
```

9. Click **Deploy** to save your function

### Create the Second Lambda Function (CheckInventory)

1. Click **Functions** in the left navigation to return to the functions list
2. Click **Create function**
3. For **Function name**, enter `lab16-CheckInventory`
4. For **Runtime**, select **Python 3.9**
5. Under **Permissions**, keep the default selection
6. Click **Create function**
7. In the function code editor, replace the code with:

```python
import random

def lambda_handler(event, context):
    # Extract items from the input
    order_id = event.get('order_id', 'unknown')
    items = event.get('items', [])
    
    # Check inventory for each item (simulated)
    inventory_status = []
    all_available = True
    
    for item in items:
        # Simulate inventory check with 80% availability
        is_available = random.random() < 0.8
        
        inventory_status.append({
            'item_id': item.get('item_id', 'unknown'),
            'available': is_available,
            'quantity_requested': item.get('quantity', 0)
        })
        
        if not is_available:
            all_available = False
    
    # Return the inventory check results
    return {
        'order_id': order_id,
        'inventory_status': inventory_status,
        'all_items_available': all_available,
        'message': 'Inventory check completed'
    }
```

8. Click **Deploy** to save your function

### Create the Third Lambda Function (ProcessPayment)

1. Return to the functions list and click **Create function**
2. For **Function name**, enter `lab16-ProcessPayment`
3. For **Runtime**, select **Python 3.9**
4. Under **Permissions**, keep the default selection
5. Click **Create function**
6. In the function code editor, replace the code with:

```python
import random

def lambda_handler(event, context):
    # Extract payment details from the input
    order_id = event.get('order_id', 'unknown')
    customer_id = event.get('customer_id', 'unknown')
    total_amount = event.get('total_amount', 0)
    
    # Process the payment (simulated)
    # 90% chance of payment success
    payment_success = random.random() < 0.9
    
    if payment_success:
        return {
            'order_id': order_id,
            'customer_id': customer_id,
            'payment_status': 'succeeded',
            'transaction_id': f'txn_{order_id}_{int(random.random() * 1000000)}',
            'amount': total_amount,
            'message': 'Payment processed successfully'
        }
    else:
        # Raise an exception to demonstrate error handling in Step Functions
        raise Exception('Payment processing failed: Insufficient funds or invalid payment method')
```

7. Click **Deploy** to save your function

### Create the Fourth Lambda Function (NotifyCustomer)

1. Return to the functions list and click **Create function**
2. For **Function name**, enter `lab16-NotifyCustomer`
3. For **Runtime**, select **Python 3.9**
4. Under **Permissions**, keep the default selection
5. Click **Create function**
6. In the function code editor, replace the code with:

```python
def lambda_handler(event, context):
    # Extract details from the input
    order_id = event.get('order_id', 'unknown')
    customer_id = event.get('customer_id', 'unknown')
    payment_status = event.get('payment_status', 'unknown')
    
    # Get outcome message based on previous steps
    if payment_status == 'succeeded':
        message = f"Thank you for your order! Your order {order_id} has been confirmed and payment processed successfully."
    else:
        message = f"There was an issue processing your order {order_id}. Please contact customer support."
    
    # In a real scenario, this would send an email, SMS, or push notification
    
    # Return notification details
    return {
        'order_id': order_id,
        'customer_id': customer_id,
        'notification_sent': True,
        'notification_type': 'email',
        'message': message,
        'notification_status': 'delivered'
    }
```

7. Click **Deploy** to save your function

## 🌐 Step 2: Create a Step Functions State Machine

Now, let's create a state machine that orchestrates these Lambda functions into a workflow:

1. Navigate to the **Step Functions** service
2. Click **Create state machine**
3. Under **Choose authoring method**, select **Design your workflow visually**
4. Under **Type**, select **Standard**
5. Click **Next**
6. You'll now be in the Workflow Studio, a visual editor for creating state machines

### Configure the Workflow

1. From the **Actions** panel on the left, find the **Flow** section and drag a **Pass** state onto the canvas
2. Select the Pass state and in the configuration panel on the right:
   - Set **State name** to `InitializeOrder`
   - Under **Input/Output**, select **Output**
   - In the **Result** field, enter:
     ```json
     {
       "order_id": "ORD-123456",
       "customer_id": "CUST-7890",
       "items": [
         {
           "item_id": "ITEM-1",
           "name": "Widget A",
           "price": 25.99,
           "quantity": 2
         },
         {
           "item_id": "ITEM-2",
           "name": "Widget B",
           "price": 10.99,
           "quantity": 1
         }
       ]
     }
     ```

3. From the **Actions** panel, find the **AWS Lambda Invoke** action and drag it onto the canvas, connecting it after the InitializeOrder state
4. Select this Lambda state and in the configuration panel:
   - Set **State name** to `ProcessOrder`
   - For **Function name**, select the `lab16-ProcessOrder` function you created earlier
   - Keep the default settings for the rest

5. Add another Lambda function by dragging the **AWS Lambda Invoke** action onto the canvas, connecting it after the ProcessOrder state
6. Configure this state:
   - Set **State name** to `CheckInventory`
   - For **Function name**, select the `lab16-CheckInventory` function
   - Keep the default settings for the rest

7. From the **Flow** section, drag a **Choice** state onto the canvas after the CheckInventory state
8. Configure this Choice state:
   - Set **State name** to `IsInventoryAvailable`
   - Click **Add choice rule**
   - For **Rule #1**:
     - Set **Variable** to `$.all_items_available`
     - Set **Operator** to `is equal to`
     - Set **Value** to `true`
     - In the **Then next state is:** dropdown, keep it as **[Create new state]** for now

9. Add another Lambda function by dragging the **AWS Lambda Invoke** action to connect to the "true" branch of the Choice state
10. Configure this state:
    - Set **State name** to `ProcessPayment`
    - For **Function name**, select the `lab16-ProcessPayment` function
    - Under **Error handling**, click **Add new catcher**
    - For the new error catcher:
      - Set **Errors** to `States.ALL` (to catch all errors)
      - Set **Fallback state** to **[Create new state]** for now

11. From the **Flow** section, drag a **Pass** state onto the canvas to connect to the error branch from ProcessPayment
12. Configure this state:
    - Set **State name** to `HandlePaymentFailure`
    - Under **Input/Output**, select **Output**
    - In the **Result** field, enter:
      ```json
      {
        "order_id.$": "$.order_id",
        "customer_id.$": "$.customer_id",
        "payment_status": "failed",
        "error_message": "Payment processing failed"
      }
      ```

13. Add another Lambda function after the ProcessPayment state
14. Configure this state:
    - Set **State name** to `NotifyCustomer`
    - For **Function name**, select the `lab16-NotifyCustomer` function
    - Keep the default settings for the rest

15. Connect the HandlePaymentFailure state to the NotifyCustomer state

16. From the **Flow** section, drag a **Pass** state onto the canvas to connect to the "false" branch of the IsInventoryAvailable Choice state
17. Configure this state:
    - Set **State name** to `HandleInventoryShortage`
    - Under **Input/Output**, select **Output**
    - In the **Result** field, enter:
      ```json
      {
        "order_id.$": "$.order_id",
        "customer_id.$": "$.customer_id",
        "payment_status": "cancelled",
        "error_message": "One or more items are out of stock"
      }
      ```
    - Connect this state to the NotifyCustomer state

18. From the **Flow** section, drag a **Succeed** state onto the canvas after the NotifyCustomer state
19. Configure this state:
    - Set **State name** to `OrderCompleted`

## 🔄 Step 3: Create and Configure IAM Role for State Machine

Before saving your state machine, you need to set up an IAM role:

1. Click **Next** to proceed to the **Review generated code** screen
2. Review the Amazon States Language (ASL) definition that was created based on your visual workflow
3. Click **Next**
4. In the **Specify state machine settings** screen:
   - For **State machine name**, enter `lab16-OrderProcessingWorkflow`
   - For **Permissions**, select **Create new role**
   - Keep the default name for the role
5. Click **Create state machine**

## 🧪 Step 4: Test the State Machine

Let's execute the state machine to see it in action:

1. On the state machine details page, click **Start execution**
2. In the input field, you can use the default input (which will be ignored since we have a hardcoded input in our InitializeOrder state) or enter:
   ```json
   {}
   ```
3. Click **Start execution**
4. The execution will begin, and you'll see a visual representation of the workflow as it progresses
5. Watch as each state is executed, and hover over states to see their input/output data
6. After the execution completes, click on each state to see the details of its execution, including input, output, and any errors

## 🔄 Step 5: Explore and Modify the Workflow

Now, let's explore and modify our workflow to learn more about Step Functions features:

1. Return to your state machine and click **Edit**
2. Click **Workflow Studio** to return to the visual editor

### Add a Parallel State

1. Let's add a Parallel state to simulate concurrent processing. Delete the connection between ProcessOrder and CheckInventory
2. From the **Flow** section, drag a **Parallel** state onto the canvas and connect it after ProcessOrder
3. Configure this state:
   - Set **State name** to `ParallelProcessing`
4. Drag the CheckInventory state to inside the first branch of the Parallel state
5. From the **Actions** panel, find the **AWS Lambda Invoke** action and drag it inside the second branch of the Parallel state
6. Configure this new Lambda state:
   - Set **State name** to `CalculateTax`
   - For this example, we'll reuse an existing function. Select the `lab16-ProcessOrder` Lambda function
7. Connect the output of the Parallel state to the IsInventoryAvailable Choice state

### Add a Wait State

1. Let's add a Wait state to simulate a delay. Add a new state between NotifyCustomer and OrderCompleted
2. From the **Flow** section, drag a **Wait** state onto the canvas
3. Configure this state:
   - Set **State name** to `WaitForProcessing`
   - Set **Wait time** to **Seconds**
   - Enter `5` for the seconds value

4. Click **Apply and exit** to save your changes
5. Go through the review steps again and click **Save** to update your state machine

## 🧪 Step 6: Test the Modified State Machine

1. Click **Start execution** to test your modified workflow
2. Use the default input or enter:
   ```json
   {}
   ```
3. Click **Start execution**
4. Observe the execution of your modified workflow, noting how the Parallel state executes both branches concurrently and how the Wait state introduces a delay

## 📊 Step 7: Monitor State Machine Executions

Let's explore the monitoring capabilities of Step Functions:

1. Navigate to your state machine's detail page
2. Click on the **Executions** tab to see all past executions
3. Select one of your executions to view its details
4. Explore the **Execution event history** tab to see a detailed log of every step in the execution
5. Check the **Execution input and output** section to see the overall input and output of your state machine

## 🧹 Step 8: Console Implementation Cleanup

To avoid ongoing charges, clean up the resources created in the console part of this lab:

1. Navigate to the **Step Functions** service
2. Select your state machine (`lab16-OrderProcessingWorkflow`)
3. Click **Delete**
4. Confirm the deletion by typing the name of the state machine

5. Navigate to the **Lambda** service
6. Select all the functions you created for this lab:
   - `lab16-ProcessOrder`
   - `lab16-CheckInventory`
   - `lab16-ProcessPayment`
   - `lab16-NotifyCustomer`
7. Click **Actions** and select **Delete**
8. Confirm the deletion

9. Navigate to the **IAM** service
10. Click on **Roles** in the left navigation
11. Find and select the roles created for your Lambda functions and Step Functions state machine (they will typically have names starting with "lab16" or contain "StepFunctions")
12. Click **Delete role** for each role
13. Confirm the deletion 

---

# 💻 PART 2: AWS CLI IMPLEMENTATION

In this second part of the lab, you'll create and configure Step Functions resources using the AWS Command Line Interface (CLI).

## 📊 Step 1: Create Lambda Functions for Workflow Steps (CLI)

Let's create the Lambda functions needed for our workflow using the AWS CLI:

```bash
# Create a directory for our Lambda function code
mkdir -p lambda-functions/lab16-cli
cd lambda-functions/lab16-cli

# Create the ProcessOrder Lambda function
cat > process_order.py << 'EOL'
def lambda_handler(event, context):
    # Extract order details from the input
    order_id = event.get('order_id', 'unknown')
    customer_id = event.get('customer_id', 'unknown')
    order_items = event.get('items', [])
    
    # Process the order (in a real scenario, this would do more)
    total_amount = sum(item.get('price', 0) * item.get('quantity', 0) for item in order_items)
    
    # Return the processed order information
    return {
        'order_id': order_id,
        'customer_id': customer_id,
        'items': order_items,
        'total_amount': total_amount,
        'status': 'processed',
        'order_processed': True,
        'message': f'Order {order_id} processed successfully'
    }
EOL

# Zip the function code
zip process_order.zip process_order.py

# Create the ProcessOrder Lambda function with AWS CLI
aws lambda create-function \
  --function-name lab16-cli-ProcessOrder \
  --runtime python3.9 \
  --handler process_order.lambda_handler \
  --zip-file fileb://process_order.zip \
  --role "$(aws iam create-role \
      --role-name lab16-cli-lambda-role \
      --assume-role-policy-document '{
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Principal": {
              "Service": "lambda.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
          }
        ]
      }' \
      --query 'Role.Arn' --output text)" \
  --region eu-west-1

# Attach the basic execution policy to the role
aws iam attach-role-policy \
  --role-name lab16-cli-lambda-role \
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole

# Create the CheckInventory Lambda function
cat > check_inventory.py << 'EOL'
import random

def lambda_handler(event, context):
    # Extract items from the input
    order_id = event.get('order_id', 'unknown')
    items = event.get('items', [])
    
    # Check inventory for each item (simulated)
    inventory_status = []
    all_available = True
    
    for item in items:
        # Simulate inventory check with 80% availability
        is_available = random.random() < 0.8
        
        inventory_status.append({
            'item_id': item.get('item_id', 'unknown'),
            'available': is_available,
            'quantity_requested': item.get('quantity', 0)
        })
        
        if not is_available:
            all_available = False
    
    # Return the inventory check results
    return {
        'order_id': order_id,
        'inventory_status': inventory_status,
        'all_items_available': all_available,
        'message': 'Inventory check completed'
    }
EOL

# Zip the function code
zip check_inventory.zip check_inventory.py

# Create the CheckInventory Lambda function
aws lambda create-function \
  --function-name lab16-cli-CheckInventory \
  --runtime python3.9 \
  --handler check_inventory.lambda_handler \
  --zip-file fileb://check_inventory.zip \
  --role "$(aws iam get-role --role-name lab16-cli-lambda-role --query 'Role.Arn' --output text)" \
  --region eu-west-1

# Create the ProcessPayment Lambda function
cat > process_payment.py << 'EOL'
import random

def lambda_handler(event, context):
    # Extract payment details from the input
    order_id = event.get('order_id', 'unknown')
    customer_id = event.get('customer_id', 'unknown')
    total_amount = event.get('total_amount', 0)
    
    # Process the payment (simulated)
    # 90% chance of payment success
    payment_success = random.random() < 0.9
    
    if payment_success:
        return {
            'order_id': order_id,
            'customer_id': customer_id,
            'payment_status': 'succeeded',
            'transaction_id': f'txn_{order_id}_{int(random.random() * 1000000)}',
            'amount': total_amount,
            'message': 'Payment processed successfully'
        }
    else:
        # Raise an exception to demonstrate error handling in Step Functions
        raise Exception('Payment processing failed: Insufficient funds or invalid payment method')
EOL

# Zip the function code
zip process_payment.zip process_payment.py

# Create the ProcessPayment Lambda function
aws lambda create-function \
  --function-name lab16-cli-ProcessPayment \
  --runtime python3.9 \
  --handler process_payment.lambda_handler \
  --zip-file fileb://process_payment.zip \
  --role "$(aws iam get-role --role-name lab16-cli-lambda-role --query 'Role.Arn' --output text)" \
  --region eu-west-1

# Create the NotifyCustomer Lambda function
cat > notify_customer.py << 'EOL'
def lambda_handler(event, context):
    # Extract details from the input
    order_id = event.get('order_id', 'unknown')
    customer_id = event.get('customer_id', 'unknown')
    payment_status = event.get('payment_status', 'unknown')
    
    # Get outcome message based on previous steps
    if payment_status == 'succeeded':
        message = f"Thank you for your order! Your order {order_id} has been confirmed and payment processed successfully."
    else:
        message = f"There was an issue processing your order {order_id}. Please contact customer support."
    
    # In a real scenario, this would send an email, SMS, or push notification
    
    # Return notification details
    return {
        'order_id': order_id,
        'customer_id': customer_id,
        'notification_sent': True,
        'notification_type': 'email',
        'message': message,
        'notification_status': 'delivered'
    }
EOL

# Zip the function code
zip notify_customer.zip notify_customer.py

# Create the NotifyCustomer Lambda function
aws lambda create-function \
  --function-name lab16-cli-NotifyCustomer \
  --runtime python3.9 \
  --handler notify_customer.lambda_handler \
  --zip-file fileb://notify_customer.zip \
  --role "$(aws iam get-role --role-name lab16-cli-lambda-role --query 'Role.Arn' --output text)" \
  --region eu-west-1

echo "All Lambda functions created successfully"
```

## 🌐 Step 2: Create an IAM Role for Step Functions (CLI)

Now, create an IAM role that grants Step Functions permission to invoke your Lambda functions:

```bash
# Create a file with the trust policy for Step Functions
cat > step_functions_trust_policy.json << EOL
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "states.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
EOL

# Create the IAM role for Step Functions
aws iam create-role \
  --role-name lab16-cli-step-functions-role \
  --assume-role-policy-document file://step_functions_trust_policy.json

# Create a policy document that grants permissions to invoke Lambda functions
cat > step_functions_policy.json << EOL
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "lambda:InvokeFunction"
      ],
      "Resource": [
        "arn:aws:lambda:eu-west-1:$(aws sts get-caller-identity --query 'Account' --output text):function:lab16-cli-*"
      ]
    }
  ]
}
EOL

# Create the IAM policy
aws iam create-policy \
  --policy-name lab16-cli-step-functions-policy \
  --policy-document file://step_functions_policy.json

# Attach the policy to the role
aws iam attach-role-policy \
  --role-name lab16-cli-step-functions-role \
  --policy-arn arn:aws:iam::$(aws sts get-caller-identity --query 'Account' --output text):policy/lab16-cli-step-functions-policy

echo "IAM role and policy created for Step Functions"
```

## 📋 Step 3: Define the State Machine (CLI)

Let's define the state machine that will orchestrate our Lambda functions:

```bash
# Store the Lambda function ARNs as variables for easy reference
PROCESS_ORDER_ARN=$(aws lambda get-function --function-name lab16-cli-ProcessOrder --query 'Configuration.FunctionArn' --output text)
CHECK_INVENTORY_ARN=$(aws lambda get-function --function-name lab16-cli-CheckInventory --query 'Configuration.FunctionArn' --output text)
PROCESS_PAYMENT_ARN=$(aws lambda get-function --function-name lab16-cli-ProcessPayment --query 'Configuration.FunctionArn' --output text)
NOTIFY_CUSTOMER_ARN=$(aws lambda get-function --function-name lab16-cli-NotifyCustomer --query 'Configuration.FunctionArn' --output text)

# Create a state machine definition file
cat > order_processing_workflow.json << EOL
{
  "Comment": "Order Processing Workflow created with AWS CLI",
  "StartAt": "InitializeOrder",
  "States": {
    "InitializeOrder": {
      "Type": "Pass",
      "Result": {
        "order_id": "ORD-123456",
        "customer_id": "CUST-7890",
        "items": [
          {
            "item_id": "ITEM-1",
            "name": "Widget A",
            "price": 25.99,
            "quantity": 2
          },
          {
            "item_id": "ITEM-2",
            "name": "Widget B",
            "price": 10.99,
            "quantity": 1
          }
        ]
      },
      "Next": "ProcessOrder"
    },
    "ProcessOrder": {
      "Type": "Task",
      "Resource": "${PROCESS_ORDER_ARN}",
      "Next": "ParallelProcessing"
    },
    "ParallelProcessing": {
      "Type": "Parallel",
      "Branches": [
        {
          "StartAt": "CheckInventory",
          "States": {
            "CheckInventory": {
              "Type": "Task",
              "Resource": "${CHECK_INVENTORY_ARN}",
              "End": true
            }
          }
        },
        {
          "StartAt": "CalculateTax",
          "States": {
            "CalculateTax": {
              "Type": "Task",
              "Resource": "${PROCESS_ORDER_ARN}",
              "End": true
            }
          }
        }
      ],
      "Next": "IsInventoryAvailable"
    },
    "IsInventoryAvailable": {
      "Type": "Choice",
      "Choices": [
        {
          "Variable": "$.all_items_available",
          "BooleanEquals": true,
          "Next": "ProcessPayment"
        }
      ],
      "Default": "HandleInventoryShortage"
    },
    "ProcessPayment": {
      "Type": "Task",
      "Resource": "${PROCESS_PAYMENT_ARN}",
      "Next": "NotifyCustomer",
      "Catch": [
        {
          "ErrorEquals": ["States.ALL"],
          "Next": "HandlePaymentFailure"
        }
      ]
    },
    "HandlePaymentFailure": {
      "Type": "Pass",
      "Result": {
        "payment_status": "failed",
        "error_message": "Payment processing failed"
      },
      "ResultPath": "$",
      "Next": "NotifyCustomer"
    },
    "HandleInventoryShortage": {
      "Type": "Pass",
      "Result": {
        "payment_status": "cancelled",
        "error_message": "One or more items are out of stock"
      },
      "ResultPath": "$",
      "Next": "NotifyCustomer"
    },
    "NotifyCustomer": {
      "Type": "Task",
      "Resource": "${NOTIFY_CUSTOMER_ARN}",
      "Next": "WaitForProcessing"
    },
    "WaitForProcessing": {
      "Type": "Wait",
      "Seconds": 5,
      "Next": "OrderCompleted"
    },
    "OrderCompleted": {
      "Type": "Succeed"
    }
  }
}
EOL

# Create the Step Functions state machine
STEP_FUNCTIONS_ROLE_ARN=$(aws iam get-role --role-name lab16-cli-step-functions-role --query 'Role.Arn' --output text)

aws stepfunctions create-state-machine \
  --name lab16-cli-OrderProcessingWorkflow \
  --definition file://order_processing_workflow.json \
  --role-arn $STEP_FUNCTIONS_ROLE_ARN \
  --region eu-west-1

echo "Step Functions state machine created successfully"
```

## 🧪 Step 4: Test the State Machine (CLI)

Now let's execute the state machine to see it in action:

```bash
# Get the ARN of the state machine
STATE_MACHINE_ARN=$(aws stepfunctions list-state-machines --query "stateMachines[?name=='lab16-cli-OrderProcessingWorkflow'].stateMachineArn" --output text)

# Start an execution of the state machine
EXECUTION_ARN=$(aws stepfunctions start-execution \
  --state-machine-arn $STATE_MACHINE_ARN \
  --input '{}' \
  --query 'executionArn' \
  --output text)

echo "Started execution with ARN: $EXECUTION_ARN"

# Wait a moment to let the execution progress
echo "Waiting for execution to progress..."
sleep 5

# Check the status of the execution
aws stepfunctions describe-execution \
  --execution-arn $EXECUTION_ARN \
  --query '{Status: status, StartDate: startDate}'

# To see details of the execution history (useful for debugging)
aws stepfunctions get-execution-history \
  --execution-arn $EXECUTION_ARN \
  --max-results 10 \
  --query 'events[*].{Type: type, Details: stateEnteredEventDetails || stateExitedEventDetails || taskScheduledEventDetails || taskSucceededEventDetails || taskFailedEventDetails || executionStartedEventDetails}'
```

## 📊 Step 5: Monitor Execution (CLI)

Let's monitor the execution until it completes:

```bash
# Loop to check the status until the execution completes
EXECUTION_STATUS="RUNNING"
echo "Monitoring execution status..."

while [ "$EXECUTION_STATUS" == "RUNNING" ]; do
  EXECUTION_STATUS=$(aws stepfunctions describe-execution \
    --execution-arn $EXECUTION_ARN \
    --query 'status' \
    --output text)
  
  echo "Current status: $EXECUTION_STATUS"
  
  if [ "$EXECUTION_STATUS" == "RUNNING" ]; then
    echo "Execution still in progress. Waiting..."
    sleep 5
  fi
done

# Get the output of the execution
if [ "$EXECUTION_STATUS" == "SUCCEEDED" ]; then
  echo "Execution completed successfully!"
  aws stepfunctions describe-execution \
    --execution-arn $EXECUTION_ARN \
    --query 'output' \
    --output text | jq .
else
  echo "Execution failed or was aborted. Check the execution history for details."
fi
```

## 🧹 Step 6: CLI Implementation Cleanup

To avoid ongoing charges, clean up the resources created in the CLI part of this lab:

```bash
# Delete the Step Functions state machine
aws stepfunctions delete-state-machine \
  --state-machine-arn $STATE_MACHINE_ARN

# Delete all Lambda functions
for func in lab16-cli-ProcessOrder lab16-cli-CheckInventory lab16-cli-ProcessPayment lab16-cli-NotifyCustomer; do
  aws lambda delete-function --function-name $func
  echo "Deleted Lambda function: $func"
done

# Detach policies from IAM roles
aws iam detach-role-policy \
  --role-name lab16-cli-step-functions-role \
  --policy-arn arn:aws:iam::$(aws sts get-caller-identity --query 'Account' --output text):policy/lab16-cli-step-functions-policy

aws iam detach-role-policy \
  --role-name lab16-cli-lambda-role \
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole

# Delete IAM policies
aws iam delete-policy \
  --policy-arn arn:aws:iam::$(aws sts get-caller-identity --query 'Account' --output text):policy/lab16-cli-step-functions-policy

# Delete IAM roles
aws iam delete-role --role-name lab16-cli-step-functions-role
aws iam delete-role --role-name lab16-cli-lambda-role

# Clean up local files
cd ../..
rm -rf lambda-functions

echo "Cleanup complete!"
```

---

## 🧪 Optional Challenges

To deepen your understanding of Step Functions, try these additional challenges:

1. **Map State**: Modify the workflow to use a Map state to process multiple orders in parallel
2. **Callback Pattern**: Implement a human approval step using the callback pattern
3. **Error Handling**: Add custom error handling with retry logic for specific error types
4. **Service Integrations**: Use direct service integrations with other AWS services like DynamoDB or SQS
5. **Express Workflow**: Create an Express workflow for high-volume, short-duration workflows

---

## 🎓 Summary

In this lab, you've learned how to:
- Create and configure AWS Step Functions state machines
- Design workflows using different state types (Task, Choice, Parallel, etc.)
- Implement Lambda functions and integrate them with Step Functions
- Configure error handling and retry logic
- Monitor and debug state machine executions
- Deploy Step Functions workflows using both the console and AWS CLI

These skills are essential for building resilient, maintainable applications that coordinate multiple AWS services. Step Functions helps you manage complex workflows visually, reducing the amount of code you need to write and maintaining the state of your application automatically.