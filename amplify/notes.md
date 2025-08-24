# AWS Amplify Project Setup with Infrastructure as Code

This README provides a comprehensive guide to setting up an AWS Amplify project, leveraging Infrastructure as Code (IaC) principles. It covers the concepts of IaC, CloudFormation, AWS CDK, and Amplify, along with practical steps to initialize and manage an Amplify project.

## What is Infrastructure as Code (IaC)?

Infrastructure as Code (IaC) is a practice where infrastructure is provisioned and managed using code and configuration files, rather than manual processes. IaC enables:

- **Automation**: Automatically provision resources like servers, S3 buckets, or databases.
- **Scalability**: Easily scale infrastructure by modifying code.
- **Collaboration**: Store infrastructure code in version control systems (e.g., Git) for team collaboration.
- **Consistency**: Ensure identical setups across environments (e.g., development, production).

AWS provides tools like **CloudFormation**, **AWS CDK**, and **AWS Amplify** to implement IaC, each offering different levels of abstraction.

## AWS IaC Tools Overview

### 1. AWS CloudFormation

CloudFormation allows you to define AWS resources (e.g., S3 buckets, EC2 instances) in JSON or YAML configuration files. These files, called templates, are used to create a "stack" of resources.

**Example CloudFormation Template**:

Below is an example of a CloudFormation template that creates an S3 bucket with a lifecycle rule to expire objects after 3 days.

```json
{
    "Resources": {
        "HelloBucket": {
            "Type": "AWS::S3::Bucket",
            "Properties": {
                "LifecycleConfiguration": {
                    "Rules": [
                        {
                            "ExpirationInDays": 3,
                            "Status": "Enabled"
                        }
                    ]
                }
            }
        }
    },
    "Outputs": {
        "HelloBucketNameOutput": {
            "Description": "Name of the created S3 bucket",
            "Value": { "Ref": "HelloBucket" }
        }
    }
}
```

To deploy this template:
1. Save it as `template.json`.
2. Use the AWS CLI or Console to create a stack: `aws cloudformation create-stack --stack-name MyStack --template-body file://template.json`.

### 2. AWS Cloud Development Kit (CDK)

The AWS CDK is an abstraction over CloudFormation, allowing developers to write infrastructure code in programming languages like TypeScript, Python, or Java. The CDK compiles code into CloudFormation templates via a process called **synthesis**.

**Steps to Set Up AWS CDK**:
1. Install the CDK globally:
   ```bash
   sudo npm install -g aws-cdk
   ```
2. Initialize a new CDK project:
   ```bash
   cdk init app --language=typescript
   ```
3. Write infrastructure code in the `lib/` directory.
4. Synthesize the CloudFormation template:
   ```bash
   cdk synth
   ```
5. Bootstrap the CDK (one-time setup per AWS account/region):
   ```bash
   cdk bootstrap
   ```
   Bootstrapping creates an S3 bucket and IAM roles for CDK deployments.
6. Deploy the stack:
   ```bash
   cdk deploy
   ```

### 3. AWS Amplify

Amplify is a high-level abstraction over CloudFormation and CDK, designed for rapid development of serverless applications. It simplifies the setup of backend resources (e.g., APIs, authentication, storage) and integrates with front-end frameworks.

**Benefits of Amplify**:
- Manages infrastructure (e.g., API Gateway, Lambda, S3) automatically.
- Provides CLI commands for easy setup and deployment.
- Supports features like authentication, APIs, and hosting out of the box.

## Setting Up an Amplify Project

Follow these steps to create and manage an Amplify project:

1. **Install the Amplify CLI**:
   ```bash
   npm install -g @aws-amplify/cli
   ```

2. **Configure Amplify**:
   Run `amplify configure` to set up AWS credentials.

3. **Create a New Amplify Project**:
   Initialize a new project in your application directory:
   ```bash
   npx create amplify@latest
   ```
   Follow the prompts to configure the project (e.g., choose JavaScript, select AWS profile).

4. **Add a Backend Resource (e.g., Function)**:
   Define a Lambda function in the `/amplify/backend/function` directory. Below is an example:

   **/amplify/backend/function/sayHello/resource.ts**:
   ```typescript
   import { defineFunction } from '@aws-amplify/backend';

   export const sayHello = defineFunction({
       name: 'sayHello',
       entry: 'handler.ts',
       bundling: {
           minify: false
       }
   });
   ```

   **/amplify/backend/function/sayHello/handler.ts**:
   ```typescript
   import { Schema } from "../../data/resource";

   type handlerType = Schema["sayHello"]["functionHandler"];

   export const handler: handlerType = async (event, context) => {
       console.log("EVENT: \n" + JSON.stringify(event, null, 2));
       const { name } = event.arguments || {};
       return `Hello, ${name || "world"}!`;
   };
   ```

5. **Run the Amplify Sandbox**:
   Test the backend locally:
   ```bash
   npx ampx sandbox
   ```

6. **Deploy the Project**:
   Push the configuration to AWS:
   ```bash
   npx ampx deploy
   ```

7. **Clean Up**:
   Delete the sandbox environment when done:
   ```bash
   npx ampx sandbox delete
   ```

## Best Practices

- **Version Control**: Store Amplify configuration files (`amplify/`) in Git for collaboration.
- **Environment Management**: Use Amplify environments (`amplify env add`) for development, staging, and production.
- **Security**: Use IAM roles with least privilege for Amplify deployments.
- **Testing**: Test Lambda functions locally using the Amplify sandbox before deploying.
- **Monitoring**: Use AWS CloudWatch to monitor Lambda logs and performance.

## Additional Notes

- **CDK vs. Amplify**: Use CDK for fine-grained control over infrastructure. Use Amplify for rapid serverless app development with minimal configuration.
- **Troubleshooting**: If `cdk bootstrap` fails, verify AWS credentials and region settings.
- **Amplify Limitations**: Amplify abstracts much of the infrastructure, so for complex customizations, consider using CDK or CloudFormation directly.`
