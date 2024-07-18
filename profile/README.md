# Amplify

Amplify is an open source enterprise Generative AI platform for organizations. The platform has advanced Gen AI capabilities and gives you choice in which model providers you use, from Anthropic Claude to OpenAI GPT-4o. Build your own assistants, integrate your data, and share across your organization.

# Deployment Instructions

These are the deployment instructions to deploy Amplify-GenAI in your own AWS environment. This deployment will create many resources in your account. Please be aware that there are costs associated with deploying the following application.

## Prerequisites

Before you begin the deployment process, ensure you have the following prerequisites met:

- Terraform installed and configured
- Access to the AWS account with an IAM user and keys to use an AWS CLI
- AWS CLI installed and configured
- Install Python 3.11 and Python 3.10 along with pip for each verison. 
- Domain hosted in Route53 in the deployment AWS account
- Serverless Framework version 3.38.0 installed and configured  `npm install -g serverless@3.38.0`
- Docker installed and configured
- Mixpanel account set up with a project created. The Mixpanel token can be found under "Project Settings" with the name "Project Token"

## Steps

### 1. Clone Repositories

- Clone all three repositories (amplify-genai-frontend, amplify-genai-backend, and amplify-genai-iac) from `https://github.com/gaiin-platform` into the same directory on your local machine.

### 2. Terraform Initialization and Application

- Navigate to the amplify-genai-iac project on your local machine.
- Configure the Terraform variables by copying the `amplify-genai-iac/<env>/terraform.tfvars_sample` file to `amplify-genai-iac/<env>/terraform.tfvars`.
- Update the `terraform.tfvars` file with the specific values for your deployment. You will need to configure the following variables:
  - Load balancing vars: `public_subnet_cidrs`, `private_subnet_cidrs`, `alb_name`, `domain_name`, `app_route53_zone_id`
  - Cognito vars: `cognito_domain`, `cognito_route53_zone_id`
  - ECS vars: `ecs_alarm_email`
- If this is the first time you are deploying, initialize your Terraform environment using `terraform init`.
- Apply your Terraform configuration using `terraform apply`. Occassionally, you will see an "access denied" error when trying to apply a policy to a resource. You may need to run `terraform apply` again to correct this issue.

### 3. Configure Serverless Framework Variables

- After applying Terraform configurations, output the Terraform state to note the variables needed for the Serverless Framework deployment.
- Create and configure a `amplify-genai-backend/var/<env>-var.yml` file using the values from the Terraform state outputs. Use `amplify-genai-backend/<env>-var.yml-example` as a reference for the required format and variables. You will need to configure all variables under the `amplify-lambda` comment.

### 4. Backend Package Installation

- Install the necessary Serverless plugins by running the following commands in the `amplify-genai-backend` directory:

  ```sh
  npm install 
  ```

- We'll need to create to virtual environments, one for each version of python, for the serverless install environment in the 'amplify-genai-backend' directory:

  ```sh
  python3.11 -m venv .311venv
  source .311venv/bin/activate
  python --version #should return python 3.11
  pip install -r 311Requirements.txt
  deactivate

  python3.10 -m venv .310venv
  source .310venv/bin/activate
  python --version #should return python 3.10
  pip install -r 310Requirements.txt
  deactivate
  ```

- For the JavaScript dependencies, navigate to the `amplify-genai-backend/amplify-lambda-js` directory and run `npm install` to install the necessary Node.js packages.

### 5. Deploy Serverless Backend Services

- To deploy the Python 3.11 backend services using the Serverless Framework, navigate to the `amplify-genai-backend` directory.
- Execute the `serverless-compose.yml` script to deploy all backend lambdas properly via the following command:

  ```sh
  source .311venv/bin/activate
  serverless deploy --stage <env>
  deactivate
  ```

- If an individual service fails to deploy and you cannot resolve the issue through the Serverless Framework, you may need to manually delete the associated CloudFormation stack from the AWS console before retrying the deployment.
- To deploy an individual service, execute:

  ```sh
  source .311venv/bin/activate
  serverless <service-name>:deploy --stage <env>
  deactivate
  ```

- where `service-name` is the specific service key as defined in `serverless-compose.yml`.

- When all of the 3.11 Services are successfully deploy. You can continue on to install the two 3.10 Services. 

  ```sh
  cd object-access
  source .310venv/bin/activate
  serverless deploy --stage <env>
  ```
- If the object-access service completes successfully change directories into the embeddings service and deploy it as well. Because of the creation of an RDS instance, this can   take 10-15 minutes. 

  ```sh
  cd ../embedding
  serverless deploy --stage <env>
  deactivate
  ```
  

### 6. Update Task Definition and Apply Terraform Configuration

After deploying the backend services, you will need to update certain variables in your Terraform configuration:

- Obtain the following environment specific (e.g., dev, prod) variables from the deployed backend services and AWS Console:
  - `API_BASE_URL`: The base URL for your API endpoints. This should be the custom API domain within the API gateway console.
  - `ASSISTANTS_API_BASE`: The exported variable from the assistants CloudFormation stack.
  - `CHAT_ENDPOINT`: The exported variable from the amplify-js CloudFormation stack.
  - `COGNITO_CLIENT_ID`: Found in the App Client settings within the Cognito console on AWS.
  - `COGNITO_ISSUER`: The base URL for your Cognito user pool, found in the Cognito console on AWS.
  - `COGNITO_DOMAIN`: The custom Cognito domain, found in the App integration tab of the Cognito console on AWS.

- Update the `amplify-genai-iac/ev/terraform.tfvars` file with the newly obtained values for the respective variables.

- Apply the updated Terraform configuration by running `terraform apply` within the `amplify-genai-iac/<env>` project directory.

Additionally, certain secrets must be updated manually in the AWS Secrets Manager or via AWS CLI:

- Navigate to the AWS Secrets Manager and locate the secret named `amplify-app-secrets`.
- Manually update the following secret values:
  - `COGNITO_CLIENT_SECRET`: Can be found in the App Client settings within the Cognito console in AWS.
  - `NEXTAUTH_SECRET`: Generate a new random secret for NextAuth.
  - `OPENAI_API_KEY`: Insert your OpenAI API Key.

### 7. Build and Deploy to ECR

- To build the container and deploy it to the service, follow these steps:
  - Authenticate to Amazon ECR using the AWS CLI.
  - Build the Docker image from the Frontend Repository directory using this command: 

    ```sh
    docker build -t <env>-<ecr_repo_name> .
    ```

    where `env` is the deployment environment, and `ecr_repo_name` is the name given in the `amplify-genai-iac/dev/terraform.tfvars` file.

  - Tag the Docker image with the `latest` tag and a unique tag that includes the date and SHA digest of the image.
  - Push the Docker image to the Amazon ECR repository with the `docker push` command.
  - Update the ECS service to use the new Docker image by forcing a new deployment with the AWS CLI.

### 8. Configure S3, Secrets and Azure Endpoints

- Upload the `amplify-genai-backend/misc_deployment_files/base.json` file to the `amplify-<deployment>-lambda-<env>-base-prompts` S3 bucket.
- Configure the `amplify-genai-backend/misc_deployment_files/endpoints.json` file with your Azure endpoints and associated keys. Update the AWS secret titled `env-openai-endpoints`.

### 9. Clean Up

- After the deployment, remove any temporary files or secrets that were created during the process. This includes any local configuration files or sensitive information that should not be stored permanently.

## Notes

- Throughout the deployment process, ensure that you are in the correct directories when executing commands.
- Always verify the output of each command to ensure that there are no errors before proceeding to the next step.
- The NEXTAUTH_SECRET can be an arbitrary string.
- It is recommended to document the values of important variables and outputs for future reference and troubleshooting.
- Below are the currently supported models that Amplify can use. To use the Anthropic or Mistral models, you will need to deploy them in your AWS account and then update the `AVAILABLE_MODELS` environment variable in the `amplify-genai-frontend` project to:
  
  ```
  AVAILABLE_MODELS='gpt-35-turbo,gpt-4o,gpt-4-1106-Preview,anthropic.claude-instant-v1,anthropic.claude-v2:1,anthropic.claude-3-sonnet-20240229-v1:0,anthropic.claude-3-haiku-20240307-v1:0,anthropic.claude-3-opus-20240229-v1:0,mistral.mistral-7b-instruct-v0:2,mistral.mixtral-8x7b-instruct-v0:1,mistral.mistral-large-2402-v1:0'
  ```

  After the `AVAILABLE_MODELS` environment variable has been updated, you will need to redeploy the AWS ECS service.

- If you have any questions or encounter issues during the deployment process, please email amplify@vanderbilt.edu for assistance.

Copyright (c) 2024 Vanderbilt University  
Authors: Jules White, Allen Karns, Karely Rodriguez, Max Moundas
