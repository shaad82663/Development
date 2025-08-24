# AWS Amplify Interview Questions and Answers

Below are common AWS Amplify interview questions and concise, accurate answers to help you prepare for your interview.

## 1. What is AWS Amplify, and how does it differ from AWS CloudFormation and AWS CDK?

**Answer**:  
AWS Amplify is a development platform for building scalable serverless applications, providing tools and services for backend setup (e.g., APIs, authentication, storage) and front-end integration. It abstracts infrastructure management, unlike **CloudFormation**, which uses JSON/YAML templates to define resources, or **AWS CDK**, which allows developers to write infrastructure code in programming languages that synthesize into CloudFormation templates. Amplify is higher-level, focusing on rapid development, while CloudFormation and CDK offer more granular control.

## 2. How do you set up an Amplify project?

**Answer**:  
1. Install the Amplify CLI: `npm install -g @aws-amplify/cli`.  
2. Configure AWS credentials: `amplify configure`.  
3. Initialize a project: `npx create amplify@latest` and follow prompts.  
4. Add resources (e.g., `amplify add api` or `amplify add function`).  
5. Test locally with `npx ampx sandbox`.  
6. Deploy to AWS: `npx ampx deploy`.  
7. Clean up with `npx ampx sandbox delete`.

## 3. What is the purpose of `cdk bootstrap` in AWS CDK, and how does it relate to Amplify?

**Answer**:  
`cdk bootstrap` sets up an AWS environment (e.g., S3 bucket, IAM roles) for CDK deployments. It’s a one-time process per account/region. While Amplify internally uses CloudFormation (and potentially CDK), it abstracts this setup, so you don’t manually run `cdk bootstrap`. Amplify’s CLI handles similar provisioning automatically during `amplify push`.

## 4. How do you define a Lambda function in Amplify?

**Answer**:  
In the `/amplify/backend/function` directory, create a folder for the function (e.g., `sayHello`). Define the function in `resource.ts` using `defineFunction` and implement the logic in `handler.ts`. Example:

**resource.ts**:
```typescript
import { defineFunction } from '@aws-amplify/backend';
export const sayHello = defineFunction({
    name: 'sayHello',
    entry: 'handler.ts',
    bundling: { minify: false }
});
```

**handler.ts**:
```typescript
import { Schema } from "../../data/resource";
type handlerType = Schema["sayHello"]["functionHandler"];
export const handler: handlerType = async (event, context) => {
    const { name } = event.arguments || {};
    return `Hello, ${name || "world"}!`;
};
```

## 5. What are the benefits of using Amplify for serverless applications?

**Answer**:  
- **Rapid Development**: Pre-built backend services (e.g., GraphQL/REST APIs, authentication).  
- **Automation**: Manages infrastructure (e.g., Lambda, API Gateway, S3).  
- **Scalability**: Automatically scales resources based on demand.  
- **Integration**: Seamless integration with front-end frameworks (e.g., React, Vue).  
- **Local Testing**: Sandbox environment for testing backend locally.

## 6. How does Amplify handle multiple environments?

**Answer**:  
Amplify supports multiple environments (e.g., dev, prod) using `amplify env add`. Each environment has its own backend configuration stored in the `amplify/team-provider-info.json` file. Switch environments with `amplify env checkout <env-name>` and deploy with `amplify push`.

## 7. What is the Amplify sandbox, and how is it used?

**Answer**:  
The Amplify sandbox (`npx ampx sandbox`) creates a local testing environment for backend resources like APIs and Lambda functions. It simulates AWS services, allowing developers to test changes before deployment. Delete the sandbox with `npx ampx sandbox delete`.

## 8. How does Amplify integrate with front-end applications?

**Answer**:  
Amplify provides client libraries (e.g., `@aws-amplify/core`, `@aws-amplify/api`) for JavaScript, React, or other frameworks. After configuring the backend, use `amplify pull` to generate a configuration file (`aws-exports.js`) that connects the front-end to backend services like APIs or authentication.

## 9. What are some common use cases for AWS Amplify?

**Answer**:  
- Building serverless web/mobile apps with authentication (e.g., Cognito).  
- Creating GraphQL/REST APIs with AppSync or API Gateway.  
- Hosting static websites with S3 and CloudFront.  
- Managing real-time data with WebSocket-based APIs.  
- Integrating with CI/CD pipelines for automated deployments.

## 10. How would you troubleshoot a failed Amplify deployment?

**Answer**:  
- Check the Amplify CLI output for error messages.  
- Verify AWS credentials and permissions in `~/.aws/credentials`.  
- Inspect CloudFormation stack events in the AWS Console for resource creation failures.  
- Ensure the `amplify/backend` configuration is valid.  
- Use `amplify status` to check resource states.  
- Review Lambda logs in CloudWatch for runtime errors.