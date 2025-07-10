# Amplify

## Overview of Amplify

Amplify is Vanderbilt University's open-source enterprise platform for generative AI, designed to empower organizations with the ability to innovate across disciplines. By offering a flexible and cost-efficient chat-based interface, Amplify allows users to experiment with and deploy generative AI solutions tailored to their specific needs. Its architecture supports vendor independence, ensuring that users can select and switch between a variety of AI models, such as those from OpenAI and Anthropic, without being locked into a single provider.

## Key Use Cases

Amplify has been deployed in diverse contexts, including:
* **Higher Education**: Streamlining content creation, such as generating quizzes, study guides, or visual aids for lectures.
* **Administrative Operations**: Automating tasks like policy summarization and contract review to improve efficiency.
* **Cross-Disciplinary Research**: Enabling teams from various fields to experiment with generative AI applications, such as data visualization or text generation, while maintaining control over their data.
Amplify’s customization options, such as creating reusable templates and domain-specific assistants, make it an adaptable tool for institutional needs.

## Open-Source Advantage

As an open-source platform, Amplify offers the following benefits:
* **Modification**: Users can tailor the platform to suit unique requirements.
* **Accessibility**: Organizations can deploy Amplify in their own AWS environments, reducing dependency on external providers.
* **Community-Driven Improvements**: Amplify’s open-source model fosters a collaborative ecosystem where users can contribute enhancements and share best practices.

## Amplify Costs & Pricing Overview

Amplify is an **open-source platform**, meaning there are no licensing fees to use it. However, users must cover costs associated with **hosting** and **AI token** usage based on the cloud provider and AI models.

### Estimating Costs

Please Note: the minimum cost associated with hosting an instance of Amplify with no usage is ~$250/mo. This is due to the fact that there are some provisioned resources in AWS do not scale completely to zero, and there is some cost associated with keeping the instance alive.

* Hosting Costs:
  * Users are responsible for paying for AWS costs associated with hosting Amplify.
  * Cost may vary based on usage and configuration.
* AI Token Usage Costs:
  * Amplify connects to AI models that charge per 1,000 input and output tokens (a token is approximately 4 characters of text).
  * Costs depend on the AI model used in your conversations.

For more details on pricing, refer to:

🔗 [AWS Bedrock Pricing](https://aws.amazon.com/bedrock/pricing/)

🔗 [OpenAI Pricing](https://openai.com/pricing/)

🔗 [Azure OpenAI Pricing](https://azure.microsoft.com/en-us/products/cognitive-services/openai-service/#pricing)

---

# Getting Started With Amplify

## Architecture

<a href="Amplify_GenAI_Diag.png" target="_blank">
  <img src="Amplify_GenAI_Diag.png" alt="Amplify GenAI Architecture" width="450" height="326">
</a>

## System Requirements
To deploy and operate Amplify, the following system prerequisites are recommended:
* **Hardware/Software Requirements**:
  * AWS account with appropriate IAM permissions
  * Terraform installed and configured
  * Python 3.11, pip, and the Serverless Framework (version 3.38.0)
  * Docker installed and configured
  * Domain hosted in Amazon Route 53
* **Integration Requirements**:
  * Mixpanel account for analytics
  * Model access enabled in the AWS Bedrock console for chosen Bedrock-hosted models

---
# How To Use Amplify

* [Using The Chat Interface](../docs/chat-interface.md)
* [Conversations View](../docs/conversation-view.md)
* [Sharing View](../docs/sharing-view.md)
* Workspace View [Deprecated]
* [Assistants, Templates, and Instructions](../docs/assistants-templates-instructions.md)
* [How To Share in Amplify](../docs//sharing.md)

---

## Setting Up Billing for Amplify’s Supported AI Models

Amplify is open-source and free to use, but users are responsible for AI model costs incurred through Amazon Bedrock, OpenAI, or Azure OpenAI. This guide walks you through setting up billing for these services to ensure uninterrupted access to AI-powered features.

### Setting Up Billing for Amazon Bedrock
Amazon Bedrock provides access to models from Amazon, Anthropic, Meta, Mistral, and other provider models. To enable billing for AI model usage:

**Step 1: Enable Amazon Bedrock in AWS**
* Sign in to your AWS Management Console.
* Navigate to Amazon Bedrock: [AWS Bedrock Console](https://aws.amazon.com/bedrock/).
* Click "Enable Bedrock" if not already activated.

**Step 2: Set Up Billing**
* Go to AWS Billing Dashboard: [AWS Billing](https://console.aws.amazon.com/billing/home).
* Under "Billing Preferences", set up a payment method.
* Enable Cost Alerts to track usage.

**Step 3: Assign IAM Permissions (If Needed)**
* Ensure your AWS user role has "Bedrock:InvokeModel" permissions to use models.
* Use the IAM console to grant access to specific teams.

### Setting Up Billing for OpenAI API (GPT Models)
To use OpenAI’s GPT models in Amplify, you need an OpenAI API key with a valid billing account.

**Step 1: Create an OpenAI Account & API Key**
* Go to [OpenAI Platform](https://platform.openai.com/).
* Sign in or create an account.
* Navigate to API Keys under your account settings.
* Click "Create API Key", then securely store it.

**Step 2: Set Up Billing**
* Go to [OpenAI Billing](https://platform.openai.com/account/billing).
* Add a payment method.
* Set monthly usage limits to avoid unexpected charges.

**Step 3: Enable Usage Monitoring**
* Check "Usage" under OpenAI’s dashboard to track token consumption.
* If needed, set spending caps to prevent excessive costs.

### Setting Up Billing for Azure OpenAI
Azure OpenAI provides access to GPT models (GPT-3.5, GPT-4, GPT-4o, etc.) within an enterprise cloud environment.

**Step 1: Create an Azure Subscription**
* Sign in to [Azure Portal](https://portal.azure.com/).
* Go to Azure OpenAI Service and select "Create a Resource".
* Choose a region that supports OpenAI models.

**Step 2: Enable Billing**
* Go to [Azure Cost Management](https://azure.microsoft.com/en-us/products/cost-management).
* Select "Payment Methods" and add a credit card or invoice-based billing.
* Set up spending limits and cost alerts.

**Step 3: Generate an API Key**
* Navigate to Azure OpenAI Resource.
* Go to Keys and Endpoint.
* Copy your API Key and endpoint for Amplify integration.

#### Monitoring & Managing Costs
To avoid unexpected expenses, follow these best practices:

* ✔ Set up cost alerts in AWS, OpenAI, or Azure.
* ✔ Monitor token usage in your provider’s billing dashboard.
* ✔ Use lower-cost models (e.g., Claude Instant, GPT-3.5) when applicable.
* ✔ Enable spending caps to control monthly costs.

### Next Steps
Once billing is set up, you can integrate your API key with Amplify to start using AI-powered features

## Amplify Deployment Steps

These are the deployment instructions to deploy Amplify-GenAI in your own AWS environment. This deployment will create many resources in your account. Please be aware that there are costs associated with deploying the following application.

### 1. Clone Repositories

- Clone all three repositories (`amplify-genai-frontend`, `amplify-genai-backend`, and `amplify-genai-iac`) from `https://github.com/gaiin-platform` into the same directory on your local machine.

### 2. Terraform Initialization and Application

- Navigate to the `amplify-genai-iac` project on your local machine.
- Configure the Terraform variables by copying the `amplify-genai-iac/<env>/terraform.tfvars_sample` file to `amplify-genai-iac/<env>/terraform.tfvars`.
- Update the `terraform.tfvars` file with the specific values for your deployment. You will need to configure the following variables:
  - Load balancing vars: `public_subnet_cidrs`, `private_subnet_cidrs`, `alb_name`, `domain_name`, `app_route53_zone_id`
  - Cognito vars: `cognito_domain`, `cognito_route53_zone_id`
  - ECS vars: `ecs_alarm_email`
- If this is the first time you are deploying, initialize your Terraform environment by running `terraform init`.
- Apply your Terraform configuration using `terraform apply`. Occassionally, you will see an "access denied" error when trying to apply a policy to a resource. You may need to run `terraform apply` again to correct this issue.

### 3. Configure Serverless Framework Variables

- After applying Terraform configurations, save the outputs from the Terraform state. These will be used in the variables needed for the Serverless Framework deployment.
- Create and configure a `amplify-genai-backend/var/<env>-var.yml` file using the values from the Terraform outputs. Use `amplify-genai-backend/<env>-var.yml-example` as a reference for the required format and variables. You will need to configure all variables. The reference sample includes comments to denote which variable from the Terraform outputs are to be used.

### 4. Backend Package Installation

- Install the necessary Serverless plugins by running the following commands in the `amplify-genai-backend` directory:

  ```sh
  npm install 
  ```

- For the JavaScript dependencies, navigate to the `amplify-genai-backend/amplify-lambda-js` directory install the necessary Node.js packages:

  ```sh
  npm install 
  ```

- Package `markitdown` for deployment by nagivating to the `amplify-genai-backend/amplify-lambda/markitdown` directory, then run the install script (elevation may be required as sudo):

 ```sh
sudo ./markitdown.sh
 ```

### 5. Deploy Serverless Backend Services

- To deploy the Python 3.11 backend services using the Serverless Framework, navigate to the `amplify-genai-backend` directory.
- Deploy all backend lambdas by running the following commands:

  ```sh
  serverless deploy --stage <env>
  ```

- If an individual service fails to deploy, and you cannot resolve the issue through the Serverless Framework, you may need to manually delete the associated CloudFormation stack from the AWS console before retrying the deployment.

- To deploy an individual service, execute:

  ```sh
  serverless <service-name>:deploy --stage <env>
  ```

- where `service-name` is the specific service key as defined in `serverless-compose.yml`.

### 6. Update Task Definition and Apply Terraform Configuration

After deploying the backend services, you will need to update certain variables in your Terraform (IAC) configuration:

- Obtain the following environment specific (e.g., `dev`, `prod`) variables from the deployed backend services and AWS Console:
  - `API_BASE_URL`: The base URL for your API endpoints. This should be the custom API domain within the API gateway console.
  - `CHAT_ENDPOINT`: The exported variable from the `amplify-js-<env>`` CloudFormation stack.
  - `COGNITO_CLIENT_ID`: Found in the App Client settings within the Cognito console on AWS.
  - `COGNITO_ISSUER`: The base URL for your Cognito user pool, found in the Cognito console on AWS.
  - `COGNITO_DOMAIN`: The custom Cognito domain, found in the App integration tab of the Cognito console on AWS.
  - `NEXTAUTH_SECRET`: A new random secret used during the auth stage (you will use this in the next step as well)
  - `NEXTAUTH_URL`: The fully protocol and domain name for where you are hosting your installation

- Update the `amplify-genai-iac/<env>/terraform.tfvars` file with the newly obtained values for the respective variables.

- Apply the updated Terraform configuration by running `terraform apply` within the `amplify-genai-iac/<env>` project directory.

Additionally, certain secrets must be updated manually in the AWS Secrets Manager or via AWS CLI:

- Navigate to the AWS Secrets Manager and locate the secret named `amplify-app-secrets`.
- Manually update the following secret values:
  - `COGNITO_CLIENT_SECRET`: Can be found in the App Client settings within the Cognito console in AWS.
  - `NEXTAUTH_SECRET`: The random secret for next auth that you used in tfvars above.
  - `OPENAI_API_KEY`: Insert your OpenAI API Key.

### 7. Build and Deploy to ECR

- To build the container and deploy it to the service, follow these steps:
  - Authenticate to Amazon ECR using the AWS CLI. (For steps, visit Amazon ECR in the AWS Management Console, select the private repository, and click `View Push Commands`.)
  - Build the Docker image from the Frontend Repository directory using this command: 

    ```sh
    docker build -t <env>-<ecr_repo_name> .
    ```

  Replace `env` with the deployment environment, and `ecr_repo_name` is the name given in the `amplify-genai-iac/<env>/terraform.tfvars` file.

  - Tag the Docker image with the `latest` tag and a unique tag that includes the date and SHA digest of the image.
  - Push the Docker image to the Amazon ECR repository with the `docker push` command.
  - Update the ECS service to use the new Docker image by forcing a new deployment with the AWS CLI or via the AWS Management Console.

### 8. Configure S3, Secrets, and Azure Endpoints

- Configure the `amplify-genai-backend/misc_deployment_files/endpoints.json` file with your Azure endpoints and associated keys. Update the AWS Secrets Manager secret titled `<env>-openai-endpoints`.

### 9. Clean Up

- After the deployment, remove any temporary files or secrets that were created during the process. This includes any local configuration files or sensitive information that should not be stored permanently.

## Notes

- Throughout the deployment process, ensure that you are in the correct directories when executing commands.
- Always verify the output of each command to ensure that there are no errors before proceeding to the next step.
- It is recommended to document the values of important variables and outputs for future reference and troubleshooting.
- If you have any questions or encounter issues during the deployment process, please email amplify@vanderbilt.edu for assistance.

Copyright (c) 2024-2025 Vanderbilt University  
Authors: Jules White, Allen Karns, Karely Rodriguez, Max Moundas
Contributions: Jason Bradley (AWS), Kai Xu (AWS), Maria Fassinger (AWS)
