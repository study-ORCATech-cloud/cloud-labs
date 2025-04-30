# LAB15: Deploying and Managing Applications with AWS Elastic Beanstalk

## ⚡ Lab Overview

In this lab, you'll work with **AWS Elastic Beanstalk**, a powerful platform-as-a-service (PaaS) that simplifies application deployment and management. You'll learn how to quickly deploy web applications to the AWS Cloud without having to worry about the underlying infrastructure. Elastic Beanstalk automatically handles capacity provisioning, load balancing, scaling, and application health monitoring, allowing you to focus on your application code rather than infrastructure management.

### 💼 Real-World DevOps Applications

Elastic Beanstalk is widely used in modern cloud applications for several key advantages:

- **Simplified Deployment**: Deploy applications without managing infrastructure
- **Multiple Platform Support**: Support for various programming languages and platforms
- **Integrated Monitoring**: Built-in health monitoring and logging
- **Auto Scaling**: Automatic scaling based on application demand
- **Version Management**: Easy application version control and deployment
- **Customizable Environment**: Retain full AWS resource control when needed

Common production use cases include:

- **Web Applications**: Hosting dynamic websites and web applications
- **API Backends**: Deploying API services for mobile and web clients
- **Microservices**: Running individual components of a microservices architecture
- **Dev/Test Environments**: Quickly spinning up development and testing environments
- **Continuous Deployment**: Implementing CI/CD pipelines for web applications
- **Small to Medium Applications**: Ideal for applications that don't require complex infrastructure

---

## 🎯 Objectives

By the end of this lab, you will be able to:
- Create and configure an Elastic Beanstalk application and environment
- Deploy a sample web application to Elastic Beanstalk
- Monitor application health and performance
- Update an application with a new version
- Configure environment settings and scaling options
- Implement different deployment strategies
- Clean up Elastic Beanstalk resources properly

---

## 🛠️ Prerequisites

- AWS account with administrative permissions
- Basic understanding of web application concepts
- Familiarity with Python or another programming language supported by Elastic Beanstalk
- AWS CLI installed and configured (refer to [CLI Setup Guide](../../CLI-Setup.md))

---

# 🌐 PART 1: AWS MANAGEMENT CONSOLE IMPLEMENTATION

In this first part of the lab, you'll create and configure Elastic Beanstalk resources using the AWS Management Console.

## 📊 Step 1: Create a Sample Application

Before deploying to Elastic Beanstalk, let's create a simple Flask application (Python) to deploy:

1. On your local machine, create a new directory for your application:
   ```
   mkdir eb-flask-app
   cd eb-flask-app
   ```

2. Create a file named `application.py` with the following content:
   ```python
   from flask import Flask, render_template
   
   # Create a Flask application
   application = Flask(__name__)
   
   # Define the main route
   @application.route('/')
   def index():
       return """
       <!DOCTYPE html>
       <html>
       <head>
           <title>Hello Elastic Beanstalk</title>
           <style>
               body {
                   font-family: Arial, sans-serif;
                   margin: 40px;
                   line-height: 1.6;
               }
               h1 {
                   color: #0066cc;
               }
               .container {
                   max-width: 800px;
                   margin: 0 auto;
                   padding: 20px;
                   border: 1px solid #ddd;
                   border-radius: 5px;
               }
           </style>
       </head>
       <body>
           <div class="container">
               <h1>Hello from AWS Elastic Beanstalk!</h1>
               <p>This is a sample Python Flask application deployed using AWS Elastic Beanstalk.</p>
               <p>Elastic Beanstalk makes it easy to deploy, manage, and scale web applications.</p>
           </div>
       </body>
       </html>
       """
   
   # Run the application
   if __name__ == '__main__':
       application.run(host='0.0.0.0', port=8080)
   ```

3. Create a file named `requirements.txt` with the following content:
   ```
   Flask==2.0.1
   ```

4. Create a file named `.ebignore` with the following content:
   ```
   venv/
   __pycache__/
   *.pyc
   *.pyo
   *.pyd
   .Python
   ```

5. Zip the application files:
   ```
   zip -r eb-flask-app.zip application.py requirements.txt .ebignore
   ```

## 🌐 Step 2: Create an Elastic Beanstalk Application

Now, let's create an Elastic Beanstalk application using the AWS Management Console:

1. Sign in to the AWS Management Console
2. Navigate to the **Elastic Beanstalk** service
3. If you see a welcome page, click **Get started** or **Create application**
4. In the **Create application** page, configure:
   - **Application name**: `lab15-flask-app`
   - **Platform**: Select **Python**
   - **Platform branch**: Select the latest Python platform version
   - **Application code**: Select **Upload your code**
     - Click **Choose file** and select the `eb-flask-app.zip` file you created
     - For **Version label**, enter `v1.0.0`
5. Under **Configure service access**:
   - Select **Create and use new service role**
   - Role name can remain as the default suggested by Elastic Beanstalk
   - For **EC2 key pair**, you can select an existing key pair or create a new one (optional for this lab)
6. Click **Configure more options**

## ⚙️ Step 3: Configure Environment Settings

Now, let's configure the environment:

1. In the **Configure environment** page, under **Presets**, select **Single instance (Free Tier eligible)**
2. In the **Capacity** box, click **Edit** and configure:
   - **Environment Type**: Select **Load balanced**
   - **Min instances**: `1`
   - **Max instances**: `2`
   - **Instance types**: Select `t2.micro` (free tier eligible)
   - Click **Save**
3. In the **Load balancer** box, click **Edit** and configure:
   - Leave the default settings (Application Load Balancer)
   - Click **Save**
4. In the **Rolling updates and deployments** box, click **Edit** and configure:
   - **Deployment policy**: Select **Rolling**
   - **Batch size**: Select **Percentage: 50%**
   - Click **Save**
5. In the **Monitoring** box, click **Edit** and configure:
   - Enable **Enhanced health reporting**
   - Click **Save**
6. When you've finished configuring the environment, click **Create environment**

## 🔄 Step 4: Wait for Environment Creation

1. Elastic Beanstalk will now create your environment and deploy your application
2. This process may take 5-10 minutes
3. You'll see status updates on the environment creation dashboard
4. Once complete, you'll see a green checkmark and a status of "Environment health: Ok"

## 🔍 Step 5: Access Your Deployed Application

1. When your environment is ready, Elastic Beanstalk will display a URL for your application
2. Click on the URL to open your Flask application in a web browser
3. You should see the "Hello from AWS Elastic Beanstalk!" page

## 📊 Step 6: Monitor Application Health and Metrics

Let's explore the monitoring features of Elastic Beanstalk:

1. In the Elastic Beanstalk console, select your environment
2. Click on the **Monitoring** tab
3. View the default metrics provided, including:
   - CPU Utilization
   - Network In/Out
   - Request Count and Latency
4. Click on **Health** in the navigation pane
5. Explore the enhanced health reporting data, including:
   - Overall environment health
   - Instance health status
   - Recent health events and causes

## 🔄 Step 7: Update the Application

Now, let's update our application and deploy a new version:

1. On your local machine, update the `application.py` file:
   - Find the line with `<h1>Hello from AWS Elastic Beanstalk!</h1>`
   - Change it to `<h1>Hello from AWS Elastic Beanstalk! (Updated Version)</h1>`
   - Add a new paragraph under the existing paragraphs:
     ```html
     <p>This is version 2.0 of our application deployed on: <span id="current-time"></span></p>
     <script>
         document.getElementById('current-time').textContent = new Date().toLocaleString();
     </script>
     ```

2. Create a new zip file for the updated application:
   ```
   zip -r eb-flask-app-v2.zip application.py requirements.txt .ebignore
   ```

3. In the Elastic Beanstalk console:
   - Select your application
   - Click on the **Upload and deploy** button
   - Click **Choose file** and select your updated zip file `eb-flask-app-v2.zip`
   - For **Version label**, enter `v2.0.0`
   - Click **Deploy**

4. Elastic Beanstalk will deploy the new version using the rolling deployment policy you configured
5. Once the deployment is complete, refresh your application URL to see the updated content

## ⚙️ Step 8: Configure Environment Variables

Elastic Beanstalk allows you to set environment variables for your application without changing code:

1. In the Elastic Beanstalk console, select your environment
2. In the left navigation pane, click on **Configuration**
3. In the **Software** section, click **Edit**
4. Scroll down to the **Environment properties** section
5. Add a new property:
   - **Name**: `APP_ENVIRONMENT`
   - **Value**: `production`
6. Click **Apply**
7. Elastic Beanstalk will update your environment with the new variable

## 🧹 Step 9: Console Implementation Cleanup

To avoid ongoing charges, clean up the resources created in the console part of this lab:

1. Navigate to the **Elastic Beanstalk** console
2. Select your application (`lab15-flask-app`)
3. In the top-right corner, click **Actions**
4. Select **Terminate environment**
5. Type the environment name to confirm
6. Click **Terminate**
7. Wait for the environment termination to complete
8. Once the environment is terminated, click on **Applications** in the left navigation
9. Select your application
10. Click **Actions** and select **Delete application**
11. Type the application name to confirm
12. Click **Delete**

This will terminate all associated resources, including EC2 instances, load balancers, security groups, and other components created by Elastic Beanstalk.

---

# 💻 PART 2: AWS CLI IMPLEMENTATION

In this second part of the lab, you'll create and configure Elastic Beanstalk resources using the AWS Command Line Interface (CLI).

## 📊 Step 1: Create a Sample Application (CLI)

We'll use the same Flask application, but set up the directory for the CLI deployment:

```bash
# Create a fresh directory
mkdir eb-flask-app-cli
cd eb-flask-app-cli

# Create application.py
cat > application.py << 'EOL'
from flask import Flask, render_template

# Create a Flask application
application = Flask(__name__)

# Define the main route
@application.route('/')
def index():
    return """
    <!DOCTYPE html>
    <html>
    <head>
        <title>Hello Elastic Beanstalk (CLI)</title>
        <style>
            body {
                font-family: Arial, sans-serif;
                margin: 40px;
                line-height: 1.6;
            }
            h1 {
                color: #0066cc;
            }
            .container {
                max-width: 800px;
                margin: 0 auto;
                padding: 20px;
                border: 1px solid #ddd;
                border-radius: 5px;
            }
        </style>
    </head>
    <body>
        <div class="container">
            <h1>Hello from AWS Elastic Beanstalk! (CLI Version)</h1>
            <p>This is a sample Python Flask application deployed using AWS Elastic Beanstalk with AWS CLI.</p>
            <p>This demonstrates how to automate Elastic Beanstalk deployments.</p>
        </div>
    </body>
    </html>
    """

# Run the application
if __name__ == '__main__':
    application.run(host='0.0.0.0', port=8080)
EOL

# Create requirements.txt
cat > requirements.txt << EOL
Flask==2.0.1
EOL

# Create .ebignore
cat > .ebignore << EOL
venv/
__pycache__/
*.pyc
*.pyo
*.pyd
.Python
EOL

# Create a zip file for deployment
zip -r eb-flask-app-cli.zip application.py requirements.txt .ebignore
```

## 🌐 Step 2: Create an Elastic Beanstalk Application (CLI)

```bash
# Create an Elastic Beanstalk application
aws elasticbeanstalk create-application \
  --application-name lab15-flask-app-cli \
  --description "Flask app deployed with Elastic Beanstalk and CLI" \
  --region eu-west-1

echo "Elastic Beanstalk application created"
```

## 📋 Step 3: Create an Application Version (CLI)

```bash
# Create an S3 bucket for application versions
RANDOM_STRING=$(date +%s | cut -c 5-10)
BUCKET_NAME="lab15-eb-app-versions-$RANDOM_STRING"

aws s3 mb s3://$BUCKET_NAME --region eu-west-1

# Upload the application zip to S3
aws s3 cp eb-flask-app-cli.zip s3://$BUCKET_NAME/

# Create an application version
aws elasticbeanstalk create-application-version \
  --application-name lab15-flask-app-cli \
  --version-label v1.0.0-cli \
  --description "Initial version deployed via CLI" \
  --source-bundle S3Bucket=$BUCKET_NAME,S3Key=eb-flask-app-cli.zip \
  --auto-create-application \
  --region eu-west-1

echo "Application version created"
```

## ⚙️ Step 4: Create an Elastic Beanstalk Environment with Configuration File (CLI)

Let's create a configuration file to define our environment settings:

```bash
# Create a configuration file
cat > eb-env-config.json << EOL
{
  "OptionSettings": [
    {
      "Namespace": "aws:elasticbeanstalk:environment",
      "OptionName": "EnvironmentType",
      "Value": "LoadBalanced"
    },
    {
      "Namespace": "aws:elasticbeanstalk:environment",
      "OptionName": "LoadBalancerType",
      "Value": "application"
    },
    {
      "Namespace": "aws:autoscaling:launchconfiguration",
      "OptionName": "InstanceType",
      "Value": "t2.micro"
    },
    {
      "Namespace": "aws:autoscaling:asg",
      "OptionName": "MinSize",
      "Value": "1"
    },
    {
      "Namespace": "aws:autoscaling:asg",
      "OptionName": "MaxSize",
      "Value": "2"
    },
    {
      "Namespace": "aws:elasticbeanstalk:application:environment",
      "OptionName": "APP_ENVIRONMENT",
      "Value": "production"
    },
    {
      "Namespace": "aws:elasticbeanstalk:healthreporting:system",
      "OptionName": "SystemType",
      "Value": "enhanced"
    },
    {
      "Namespace": "aws:elasticbeanstalk:updatepolicy:rollingupdate",
      "OptionName": "RollingUpdateEnabled",
      "Value": "true"
    },
    {
      "Namespace": "aws:elasticbeanstalk:updatepolicy:rollingupdate",
      "OptionName": "RollingUpdateType",
      "Value": "Health"
    },
    {
      "Namespace": "aws:elasticbeanstalk:updatepolicy:rollingupdate",
      "OptionName": "BatchSizeType",
      "Value": "Percentage"
    },
    {
      "Namespace": "aws:elasticbeanstalk:updatepolicy:rollingupdate",
      "OptionName": "BatchSize",
      "Value": "50"
    }
  ]
}
EOL

# Create the environment
aws elasticbeanstalk create-environment \
  --application-name lab15-flask-app-cli \
  --environment-name lab15-flask-env-cli \
  --description "Flask environment created with CLI" \
  --version-label v1.0.0-cli \
  --solution-stack-name "64bit Amazon Linux 2 v3.5.1 running Python 3.8" \
  --option-settings file://eb-env-config.json \
  --region eu-west-1

echo "Creating Elastic Beanstalk environment (this may take 5-10 minutes)..."
```

## 🔄 Step 5: Wait for Environment Creation (CLI)

```bash
# Check environment status
aws elasticbeanstalk describe-environments \
  --environment-names lab15-flask-env-cli \
  --region eu-west-1 \
  --query "Environments[0].{Status:Status,Health:Health,CNAME:CNAME}"

echo "Wait until the Status is Ready and Health is Green before proceeding"
```

## 🔍 Step 6: Access Your Deployed Application (CLI)

```bash
# Get the environment URL
ENVIRONMENT_URL=$(aws elasticbeanstalk describe-environments \
  --environment-names lab15-flask-env-cli \
  --region eu-west-1 \
  --query "Environments[0].CNAME" \
  --output text)

echo "Your application is available at: http://$ENVIRONMENT_URL"
```

## 🔄 Step 7: Update the Application (CLI)

```bash
# Update application.py
cat > application.py << 'EOL'
from flask import Flask, render_template

# Create a Flask application
application = Flask(__name__)

# Define the main route
@application.route('/')
def index():
    return """
    <!DOCTYPE html>
    <html>
    <head>
        <title>Hello Elastic Beanstalk (CLI)</title>
        <style>
            body {
                font-family: Arial, sans-serif;
                margin: 40px;
                line-height: 1.6;
            }
            h1 {
                color: #0066cc;
            }
            .container {
                max-width: 800px;
                margin: 0 auto;
                padding: 20px;
                border: 1px solid #ddd;
                border-radius: 5px;
            }
        </style>
    </head>
    <body>
        <div class="container">
            <h1>Hello from AWS Elastic Beanstalk! (CLI Version Updated)</h1>
            <p>This is a sample Python Flask application deployed using AWS Elastic Beanstalk with AWS CLI.</p>
            <p>This demonstrates how to automate Elastic Beanstalk deployments.</p>
            <p>This is version 2.0 of our application deployed on: <span id="current-time"></span></p>
            <script>
                document.getElementById('current-time').textContent = new Date().toLocaleString();
            </script>
        </div>
    </body>
    </html>
    """

# Run the application
if __name__ == '__main__':
    application.run(host='0.0.0.0', port=8080)
EOL

# Create a new zip file for the updated application
zip -r eb-flask-app-cli-v2.zip application.py requirements.txt .ebignore

# Upload the updated application zip to S3
aws s3 cp eb-flask-app-cli-v2.zip s3://$BUCKET_NAME/

# Create a new application version
aws elasticbeanstalk create-application-version \
  --application-name lab15-flask-app-cli \
  --version-label v2.0.0-cli \
  --description "Updated version deployed via CLI" \
  --source-bundle S3Bucket=$BUCKET_NAME,S3Key=eb-flask-app-cli-v2.zip \
  --region eu-west-1

# Update the environment to use the new version
aws elasticbeanstalk update-environment \
  --application-name lab15-flask-app-cli \
  --environment-name lab15-flask-env-cli \
  --version-label v2.0.0-cli \
  --region eu-west-1

echo "Deploying updated application version (this may take a few minutes)..."
```

## 📊 Step 8: Monitor Application Health and Metrics (CLI)

```bash
# Check environment health
aws elasticbeanstalk describe-environments \
  --environment-names lab15-flask-env-cli \
  --region eu-west-1 \
  --query "Environments[0].{Status:Status,Health:Health,HealthStatus:HealthStatus}"

# Get instance health information
aws elasticbeanstalk describe-instances-health \
  --environment-name lab15-flask-env-cli \
  --attribute-names All \
  --region eu-west-1 \
  --query "InstanceHealthList[0]"
```

## 🧹 Step 9: CLI Implementation Cleanup

```bash
# Terminate the environment
echo "Terminating Elastic Beanstalk environment..."
aws elasticbeanstalk terminate-environment \
  --environment-name lab15-flask-env-cli \
  --region eu-west-1

# Wait for environment termination to complete
echo "Waiting for environment termination (this may take a few minutes)..."
sleep 300  # Allow some time for termination to complete

# Delete the application
echo "Deleting Elastic Beanstalk application..."
aws elasticbeanstalk delete-application \
  --application-name lab15-flask-app-cli \
  --terminate-env-by-force \
  --region eu-west-1

# Empty and delete the S3 bucket
echo "Cleaning up S3 bucket..."
aws s3 rm s3://$BUCKET_NAME --recursive
aws s3 rb s3://$BUCKET_NAME --force

# Clean up local files
echo "Cleaning up local files..."
cd ..
rm -rf eb-flask-app-cli
rm -f *.zip eb-env-config.json

echo "Cleanup complete!"
```

---

## 🧪 Optional Challenges

To deepen your understanding of Elastic Beanstalk, try these additional challenges:

1. **Custom Platform**: Explore creating a custom platform with the Packer tool
2. **Multi-container Docker**: Deploy a multi-container Docker application
3. **Worker Tier**: Create a worker environment and implement background processing
4. **Blue/Green Deployment**: Implement a blue/green deployment strategy
5. **Configuration Files**: Create `.ebextensions` configuration files to customize your environment

---

## 🎓 Summary

In this lab, you've learned how to:
- Create and configure Elastic Beanstalk applications and environments
- Deploy web applications to Elastic Beanstalk
- Monitor application health and performance
- Update applications with new versions
- Configure environment settings and scaling options
- Implement deployment strategies
- Clean up Elastic Beanstalk resources

These skills are essential for implementing efficient application deployment workflows in AWS. Elastic Beanstalk's simplicity and power make it an ideal platform for hosting web applications without the complexity of managing individual infrastructure components, while still allowing for deep customization when needed. 