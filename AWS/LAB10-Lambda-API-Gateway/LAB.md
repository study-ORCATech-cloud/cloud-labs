# LAB10: Deploy a Serverless Function with API Gateway and Lambda (Console & CLI)

## ⚡ Lab Overview

In this final AWS lab, you'll build and expose a **serverless REST API** using **AWS Lambda** and **Amazon API Gateway**. You'll write a basic function that returns a JSON response and connect it to an HTTP endpoint with API Gateway, creating a fully functional API without managing any servers. This serverless architecture represents a modern approach to building cloud applications with automatic scaling, pay-per-use pricing, and minimal operational overhead.

### 💼 Real-World DevOps Applications

Serverless architectures are increasingly common in production environments for several key reasons:

- **Cost Efficiency**: You only pay for actual execution time, not idle servers
- **Scalability**: Automatic scaling from zero to thousands of concurrent executions
- **Reduced Operational Burden**: No server management, patching, or capacity planning
- **Microservices Architecture**: Enables building decoupled, single-purpose functions
- **Event-Driven Processing**: Ideal for asynchronous workflows and integrations

Common production use cases include:

- **Backend APIs for Web/Mobile Applications**: User authentication, data processing
- **Real-Time File Processing**: Image/video processing when files are uploaded to S3
- **Scheduled Tasks**: Report generation, database cleanups, monitoring
- **Webhook Receivers**: Processing events from 3rd party services (GitHub, Stripe, etc.)
- **Stream Processing**: Analyzing logs, IoT data streams, or user analytics

---

## 🎯 Objectives

By the end of this lab, you will be able to:
- Create and configure a Lambda function using the AWS Console and CLI
- Write, test, and debug a Python-based Lambda handler
- Build a RESTful API using Amazon API Gateway with proper resource paths
- Connect API Gateway endpoints to Lambda functions
- Deploy and test an API via HTTP requests
- Implement path parameters and query string handling
- Understand Lambda execution roles and security considerations
- Apply best practices for serverless function development

---

## 🛠️ Prerequisites

- AWS account with administrative permissions
- Basic understanding of RESTful APIs and HTTP methods
- Familiarity with Python programming (for the Lambda function code)
- AWS CLI installed and configured (refer to [CLI Setup Guide](../../CLI-Setup.md))
- A REST API client like Postman, curl, or a web browser for testing

---

## 🧠 Part 1: Create a Lambda Function

### Step 1: Create a Lambda Function Using the Console

1. Sign in to the AWS Management Console
2. Navigate to the **Lambda** service
3. Click **Create function**
4. Select **Author from scratch**
5. In the **Basic information** section:
   - **Function name**: `lab10-hello-world`
   - **Runtime**: Python 3.10
   - **Architecture**: x86_64
6. Under **Permissions**:
   - Expand **Change default execution role**
   - Select **Create a new role with basic Lambda permissions**
   - This role will include permissions for CloudWatch Logs
7. Leave all other settings as default and click **Create function**

### Step 2: Write and Deploy Your Lambda Function Code

Once your function is created, you'll be taken to the function configuration page:

1. In the **Code** tab, you'll see the code editor with a default `lambda_function.py` file
2. Replace the existing code with the following:

```python
import json

def lambda_handler(event, context):
    """
    Simple Lambda function that returns a Hello World message
    
    Parameters:
    - event: Contains information about the event that triggered this function
    - context: Contains information about the Lambda execution environment
    
    Returns:
    - API Gateway formatted response with JSON body
    """
    
    # Log the incoming event for debugging (will appear in CloudWatch Logs)
    print("Received event:", json.dumps(event))
    
    return {
        'statusCode': 200,
        'headers': {
            'Content-Type': 'application/json',
            'Access-Control-Allow-Origin': '*'  # For CORS support
        },
        'body': json.dumps({
            'message': 'Hello from Lambda!',
            'timestamp': str(context.aws_request_id),
            'event': event
        })
    }
```

3. Click **Deploy** to save your changes

### Step 3: Test Your Lambda Function

1. At the top of the page, click **Test**
2. If this is your first test, you'll need to configure a test event:
   - **Event name**: `TestEvent`
   - **Event JSON**: Leave the default JSON
   - Click **Save**
3. Click **Test** again to run the function with your test event
4. You should see a **Response** section with a successful execution, showing your JSON output
5. Explore the **Execution result** details, including logs and duration

---

## 🌐 Part 2: Create an API Gateway Endpoint

### Step 1: Create a New HTTP API

1. Navigate to the **API Gateway** service
2. Click **Create API**
3. Under **HTTP API**, click **Build**
4. In the **Configure API** screen:
   - **API name**: `lab10-api`
   - **Description** (optional): `API for Lambda lab functions`
5. Under **Configure routes**:
   - Click **Add route**
   - **Method**: GET
   - **Resource path**: `/hello`
   - **Integration target**: Select your Lambda function `lab10-hello-world`
6. Click **Next**
7. Under **Configure stages**:
   - **Stage name**: `dev`
   - Leave other settings as default
8. Click **Next**
9. Review your settings and click **Create**

### Step 2: Test Your API Endpoint

1. After creation, you'll see your API details including the **Invoke URL**
2. This is the base URL for your API (e.g., `https://abcdef123.execute-api.us-east-1.amazonaws.com`)
3. Test your endpoint by:
   - Opening a web browser and navigating to your Invoke URL + route (e.g., `https://abcdef123.execute-api.us-east-1.amazonaws.com/dev/hello`)
   - Or using curl from a terminal:
   ```bash
   curl https://abcdef123.execute-api.us-east-1.amazonaws.com/dev/hello
   ```
4. You should see a JSON response with your "Hello from Lambda!" message

### Step 3: Add a Path Parameter Route

Let's create a more dynamic endpoint that can accept parameters:

1. Return to the API Gateway console and select your API
2. Navigate to the **Routes** section
3. Click **Create**
4. Configure the new route:
   - **Method**: GET
   - **Path**: `/hello/{name}`
   - **Integration target**: Your `lab10-hello-world` Lambda function
5. Click **Create**

### Step 4: Update Lambda to Handle Path Parameters

Now we need to update our Lambda function to handle the name parameter:

1. Return to the Lambda console and open your `lab10-hello-world` function
2. Modify the code to handle the path parameter:

```python
import json

def lambda_handler(event, context):
    """
    Lambda function that returns a personalized hello message
    Handles both basic route and route with name parameter
    """
    
    # Log the incoming event for debugging
    print("Received event:", json.dumps(event))
    
    # Check if we have a path parameter
    path_parameters = event.get('pathParameters', {})
    
    if path_parameters and path_parameters.get('name'):
        name = path_parameters['name']
        message = f"Hello, {name}! Welcome to the serverless world!"
    else:
        message = "Hello from Lambda!"
    
    # Check if we have a query string parameter 'format'
    query_params = event.get('queryStringParameters', {}) or {}
    response_format = query_params.get('format', 'json')
    
    response_body = {
        'message': message,
        'timestamp': str(context.aws_request_id)
    }
    
    # Handle different response formats
    if response_format.lower() == 'text':
        return {
            'statusCode': 200,
            'headers': {
                'Content-Type': 'text/plain',
                'Access-Control-Allow-Origin': '*'
            },
            'body': message
        }
    else:
        return {
            'statusCode': 200,
            'headers': {
                'Content-Type': 'application/json',
                'Access-Control-Allow-Origin': '*'
            },
            'body': json.dumps(response_body)
        }
```

3. Click **Deploy** to save your changes

### Step 5: Test Your Dynamic Endpoint

1. Test the basic route again to ensure it still works
2. Test the new parameterized route by replacing `{name}` with your name:
   ```bash
   curl https://abcdef123.execute-api.us-east-1.amazonaws.com/dev/hello/YourName
   ```
3. Try adding a query parameter to change the response format:
   ```bash
   curl https://abcdef123.execute-api.us-east-1.amazonaws.com/dev/hello/YourName?format=text
   ```

---

## 💻 Part 3: Implement Lambda and API Gateway Using AWS CLI

For automation purposes, you can create the same resources using the AWS CLI:

### Step 1: Create Lambda Execution Role

Create a file named `trust-policy.json`:

```json
{
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
}
```

Create the role and attach policies:

```bash
# Create the role
ROLE_ARN=$(aws iam create-role \
  --role-name lab10-lambda-role \
  --assume-role-policy-document file://trust-policy.json \
  --query "Role.Arn" \
  --output text)

echo "Created role: $ROLE_ARN"

# Attach the necessary policy for CloudWatch Logs
aws iam attach-role-policy \
  --role-name lab10-lambda-role \
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole
```

### Step 2: Create Lambda Function

Create a file named `lambda_function.py` with the same Python code used earlier:

```python
import json

def lambda_handler(event, context):
    # Log the incoming event for debugging
    print("Received event:", json.dumps(event))
    
    # Check if we have a path parameter
    path_parameters = event.get('pathParameters', {})
    
    if path_parameters and path_parameters.get('name'):
        name = path_parameters['name']
        message = f"Hello, {name}! Welcome to the serverless world!"
    else:
        message = "Hello from Lambda!"
    
    # Check if we have a query string parameter 'format'
    query_params = event.get('queryStringParameters', {}) or {}
    response_format = query_params.get('format', 'json')
    
    response_body = {
        'message': message,
        'timestamp': str(context.aws_request_id)
    }
    
    # Handle different response formats
    if response_format.lower() == 'text':
        return {
            'statusCode': 200,
            'headers': {
                'Content-Type': 'text/plain',
                'Access-Control-Allow-Origin': '*'
            },
            'body': message
        }
    else:
        return {
            'statusCode': 200,
            'headers': {
                'Content-Type': 'application/json',
                'Access-Control-Allow-Origin': '*'
            },
            'body': json.dumps(response_body)
        }
```

Package and deploy the function:

```bash
# Create a zip package
zip function.zip lambda_function.py

# Wait a moment for the IAM role to be fully available
sleep 10

# Create the Lambda function
LAMBDA_ARN=$(aws lambda create-function \
  --function-name lab10-hello-cli \
  --runtime python3.10 \
  --role $ROLE_ARN \
  --handler lambda_function.lambda_handler \
  --zip-file fileb://function.zip \
  --query "FunctionArn" \
  --output text)

echo "Created Lambda function: $LAMBDA_ARN"
```

### Step 3: Create API Gateway via CLI

```bash
# Create an HTTP API
API_ID=$(aws apigatewayv2 create-api \
  --name lab10-api-cli \
  --protocol-type HTTP \
  --target $LAMBDA_ARN \
  --query "ApiId" \
  --output text)

echo "Created API Gateway: $API_ID"

# Get the API endpoint
API_ENDPOINT=$(aws apigatewayv2 get-api \
  --api-id $API_ID \
  --query "ApiEndpoint" \
  --output text)

echo "API Endpoint: $API_ENDPOINT"

# Add permission for API Gateway to invoke the Lambda function
aws lambda add-permission \
  --function-name lab10-hello-cli \
  --statement-id apigateway-invoke \
  --action lambda:InvokeFunction \
  --principal apigateway.amazonaws.com \
  --source-arn "arn:aws:execute-api:$(aws configure get region):$(aws sts get-caller-identity --query "Account" --output text):$API_ID/*"

# Create a route with a path parameter
aws apigatewayv2 create-route \
  --api-id $API_ID \
  --route-key "GET /hello/{name}" \
  --target "integrations/$INTEGRATION_ID"

echo "Test your API with: curl $API_ENDPOINT/hello"
echo "Test with parameter: curl $API_ENDPOINT/hello/YourName"
```

---

## 🧪 Challenge: Advanced Serverless Features

Complete these additional tasks to expand your serverless skills:

### Challenge 1: Create a POST Endpoint with Input Validation

1. Add a new route to your API Gateway:
   - Method: POST
   - Path: `/messages`
   - Integration: Your Lambda function

2. Update your Lambda function to handle POST requests and validate input:
   ```python
   # Add this to your Lambda handler
   if event.get('requestContext', {}).get('http', {}).get('method') == 'POST':
       # Extract body
       body = json.loads(event.get('body', '{}'))
       
       # Validate required fields
       if 'message' not in body:
           return {
               'statusCode': 400,
               'body': json.dumps({'error': 'Missing required field: message'})
           }
       
       # Process the message (in a real application, you might store it in DynamoDB)
       return {
           'statusCode': 201,
           'body': json.dumps({
               'status': 'Message received',
               'messageId': context.aws_request_id,
               'message': body['message']
           })
       }
   ```

3. Test with curl:
   ```bash
   curl -X POST \
     -H "Content-Type: application/json" \
     -d '{"message":"Hello, serverless world!"}' \
     https://your-api-id.execute-api.region.amazonaws.com/dev/messages
   ```

### Challenge 2: Implement Error Handling and Logging

Enhance your Lambda function with proper error handling:

```python
def lambda_handler(event, context):
    try:
        # Your existing code here
        
        # Simulate a potential error
        if event.get('queryStringParameters', {}).get('error') == 'true':
            # Intentional error for testing error handling
            raise Exception("This is a test error")
        
        # Rest of your handler code
        
    except Exception as e:
        print(f"ERROR: {str(e)}")
        return {
            'statusCode': 500,
            'body': json.dumps({
                'error': 'Internal server error',
                'message': str(e)
            })
        }
```

Test your error handling by adding `?error=true` to your API URL.

### Challenge 3: Add AWS X-Ray Tracing

Enable X-Ray tracing to gain insights into your function's performance:

1. Update your Lambda's IAM role to include the `AWSXRayDaemonWriteAccess` policy
2. Enable active tracing in the Lambda console or via CLI:
   ```bash
   aws lambda update-function-configuration \
     --function-name lab10-hello-world \
     --tracing-config Mode=Active
   ```
3. Update your API Gateway to use X-Ray tracing as well

---

## 📈 Part 4: Monitoring and Observability

### Step 1: View CloudWatch Logs

Lambda automatically logs all function output to CloudWatch Logs:

1. Navigate to **CloudWatch > Log Groups**
2. Find the log group named `/aws/lambda/lab10-hello-world`
3. Click on the log group to see log streams
4. Each stream represents a function instance
5. Click on a stream to view detailed logs

### Step 2: Monitor Function Metrics

1. In the Lambda console, select your function
2. Go to the **Monitor** tab
3. Here you can see metrics like:
   - Invocations
   - Duration
   - Error count
   - Throttles
4. You can create CloudWatch Alarms for any of these metrics

---

## 🧹 Cleanup

To avoid incurring charges after completing this lab, clean up all resources:

### Console Cleanup:

1. **Delete the API Gateway**:
   - Navigate to API Gateway
   - Select your API
   - Click **Actions** > **Delete**
   - Confirm deletion

2. **Delete the Lambda Function**:
   - Navigate to Lambda
   - Select your function
   - Click **Actions** > **Delete**
   - Confirm deletion

3. **Delete the IAM Role** (optional):
   - Navigate to IAM > Roles
   - Search for the role used by your Lambda function
   - Select it and click **Delete**
   - Confirm deletion

### CLI Cleanup:

```bash
# Delete API Gateway
aws apigatewayv2 delete-api --api-id $API_ID

# Delete Lambda Function
aws lambda delete-function --function-name lab10-hello-cli

# Detach policies from the role
aws iam detach-role-policy \
  --role-name lab10-lambda-role \
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole

# Delete the IAM role
aws iam delete-role --role-name lab10-lambda-role

# Remove local files
rm -f function.zip lambda_function.py trust-policy.json
```

---

## ✅ Key Takeaways

In this lab, you've learned:

- How to create and configure Lambda functions to run code without servers
- How to build an API using Amazon API Gateway to expose your Lambda function
- How to handle dynamic path parameters and query strings
- How to implement proper error handling and response formatting
- How to monitor your serverless application using CloudWatch Logs
- How to automate deployment using AWS CLI
- Best practices for Lambda function development and API design

Serverless architecture has become a cornerstone of modern cloud applications, offering significant advantages in terms of scalability, cost efficiency, and reduced operational overhead. The skills you've learned in this lab provide a foundation for building sophisticated, production-ready serverless applications.

## 📚 Additional Resources

- [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)
- [API Gateway Developer Guide](https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html)
- [Serverless Application Model (SAM)](https://aws.amazon.com/serverless/sam/)
- [Best Practices for Lambda Functions](https://docs.aws.amazon.com/lambda/latest/dg/best-practices.html)
- [Serverless Microservices Patterns](https://serverlessland.com/patterns)

---

Happy Serverless Computing! ⚡