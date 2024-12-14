# Assignment 2 : Full-stack Cloud-native Development: Deploying and Managing the BestBuy  


## Architecture
The application has the following services: 

   | Service            | Description                                | Github Repo                                                                 |
   |--------------------|--------------------------------------------|-----------------------------------------------------------------------------|
   | `store-front`      | Web app for customers to place orders      | [store-front-bestbuy](https://github.com/Fattehali/store-front-bestbuy.git)          |
   | `store-admin`      | Web app for store employees                | [store-admin-bestbuy](https://github.com/Fattehali/store-admin-bestbuy.git)           |
   | `order-service`    | Handles order placement                    | [order-service-bestbuy](https://github.com/Fattehali/order-service-bestbuy.git)       |
   | `product-service`  | Handles CRUD operations on products        | [product-service-bestbuy](https://github.com/Fattehali/product-service-bestbuy.git)   |
   | `makeline-service` | Processes and completes orders             | [makeline-service-bestbuy](https://github.com/Fattehali/makeline-service-bestbuy.git) |
   | `ai-service`       | AI-based product descriptions and images   | [ai-service-bestbuy](https://github.com/Fattehali/ai-service-bestbuy.git)             |
   | `virtual-customer` | Simulates customer order creation          | [virtual-customer-bestbuy](https://github.com/Fattehali/virtual-customer-bestbuy.git) |
   | `virtual-worker`   | Simulates order completion                 | [virtual-worker-bestbuy](https://github.com/Fattehali/virtual-worker-bestbuy.git)     |

## Docker repositories

   - **Table of Docker Images**:  
     - A table listing all Docker images you created, including their names and links to their Docker Hub repositories.  
     - Example:
       | **Service**         | **Docker Image Link**                     |
       |---------------------|-------------------------------------------|
       | Store-Front         | `https://hub.docker.com/repository/docker/fattehali/store-front-bb/general`                       |
       | Store-admin         | `https://hub.docker.com/repository/docker/fattehali/store-admin-bb`                       |
       | Order-service         | `https://hub.docker.com/repository/docker/fattehali/order-service`                       |
       | Product-service        | `https://hub.docker.com/repository/docker/fattehali/product-service`                       |
       | Makeline-service         | `https://hub.docker.com/repository/docker/fattehali/makeline-service`                       |
       | AI-service         | `https://hub.docker.com/repository/docker/fattehali/ai-service-bb`                        |
       | virtual-customer      | `https://hub.docker.com/repository/docker/fattehali/virtual-customer`                       |
       | virtual-worker        | `https://hub.docker.com/repository/docker/fattehali/virtual-worker`                       |
    
## Demo Link:

https://algonquinlivecom-my.sharepoint.com/:v:/g/personal/suna0011_algonquinlive_com/EXkvt_Mp0hhDm3qZghUqu3MBBAWBv-nvNCc0rPf_7zIZvQ

## Architecture Diagram

![Logical Application Architecture Diagram](assets/Algonquin%20Pet%20Store%20On%20Steroids.png)

## Run the app on Azure Kubernetes Service (AKS)

You can use the kubernetes YAML files provided in the [Deployment Files](./Deployment%20Files/) folder to deploy the app to an AKS cluster.

## Step 1: Clone the Algonquin Pet Store Repository

To begin, clone the [**Assignment-2-Full-Stack)**](git clone https://github.com/Fattehali/Assignment-2-Full-Stack.git) repository, which contains all necessary deployment files.

 **Review the Deployment Files**:
   - Navigate to the `Deployment Files` folder
   - This folder contains YAML files for deploying all necessary Kubernetes resources, including services, deployments, StatefulSets, ConfigMaps, and Secrets.


## Step 2: Set Up the AKS Cluster
Create an AKS cluster with two worker nodes steps are given below: 

Step 1: Install `kubectl`

1. **What is `kubectl`?**
   - `kubectl` is a command-line tool that allows you to communicate with and manage Kubernetes clusters. You will use `kubectl` to deploy applications, configure clusters, and inspect resources.

2. **Installing `kubectl`:**
   - Follow the official installation guide to install `kubectl` on your system. The guide provides detailed instructions for various operating systems.
   - [kubectl Installation Guide](https://kubernetes.io/docs/tasks/tools/)

3. **Verify `kubectl` Installation:**
   - After installing, confirm that `kubectl` is properly set up by running:
     ```bash
     kubectl version --client
     ```
   - You should see the client version information displayed, confirming a successful installation.

---
## Step 2: Create an Azure Kubernetes Cluster (AKS)

1. **Log in to Azure Portal:**
   - Go to [https://portal.azure.com](https://portal.azure.com) and log in with your Azure account.

2. **Create a Resource Group:**
   - In the Azure Portal, search for **Resource Groups** in the search bar.
   - Click **Create** and fill in the following:
     - **Resource group name**: `BestBuy`
     - **Region**: `Canada`.
   - Click **Review + Create** and then **Create**.

3. **Create an AKS Cluster:**
   - In the search bar, type **Kubernetes services** and click on it.
   - Click **Create** and select **Kubernetes cluster**
   - In the `Basics` tap fill in the following details:
     - **Subscription**: Select your subscription.
     - **Resource group**: Choose `BestBuy`.
     - **Cluster preset configuration**: Choose `Dev/Test`.
     - **Kubernetes cluster name**: `BestBuy`.
     - **Region**: Same as your resource group (e.g., `Canada`).
     - **Availability zones**: `None`.
     - **AKS pricing tier**: `Free`.
     - **Kubernetes version**: `Default`.
     - **Automatic upgrade**: `Disabled`.
     - **Automatic upgrade scheduler**: `No schedule`.
     - **Node security channel type**: `None`.
     - **Security channel scheduler**: `No schedule`.
     - **Authentication and Authorization**: `Local accounts with Kubernetes RBAC`.
   - In the `Node pools` tap fill in the following details:
     - Select **agentpool**. Optionally change its name to `masterpool`. This nodes will have the controlplane.
        - Set **node size** to `D2as_v4`.
        - **Scale method**: `Manual`
        - **Node count**: `1`
        - Click `update`
     - Click on **Add node pool**:
        - **Node pool name**: `workerspool`.
        - **Mode**: `User` 
        - Set **node size** to `D2as_v4`.
        - **Scale method**: `Manual`
        - **Node count**: `2`
        - Click `add`
   - Click **Review + Create**, and then **Create**. The deployment will take a few minutes.

4. **Connect to the AKS Cluster:**
   - Once the AKS cluster is deployed, navigate to the cluster in the Azure Portal.
   - In the overview page, click on **Connect**. 
   - Select **Azure CLI** tap. You will need Azure CLI. If you don't have it: [**Install Azure CLI**](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
   - Login to your azure account using the following command:
      ```
      az login
      ```
   - Set the cluster subscription using the command shown in the portal (it will look something like this):
      ```
      az account set --subscription 'subscribtion-id'
      ```

   - Copy the command shown in the portal for configuring `kubectl` (it will look something like this):
     ```
     az aks get-credentials --resource-group BestBuy --name BestBuy
     ```
      **Understanding the Command:**
      - The command `az aks get-credentials` pulls the necessary configuration files to enable `kubectl` to access your AKS cluster. Here’s a breakdown:
     - `--resource-group` specifies the resource group where your AKS cluster resides.
     - `--name` specifies the name of your AKS cluster.
     - `--overwrite-existing` can be used to overwrite any existing Kubernetes configuration files for the same cluster. This is useful if you’ve connected to the cluster before or if multiple configurations exist for it.
   - Verify Cluster Access:
      - Test your connection to the AKS cluster by listing all nodes:
        ```
        kubectl get nodes

## Step 3: Set Up the AI Backing Services
To enable AI-generated product descriptions and image generation features, you will deploy the required **Azure OpenAI Services** for GPT-4 (text generation) and DALL-E 3 (image generation). This step is essential to configure the **AI Service** component in the BestBuy.

### Task 1: Create an Azure OpenAI Service Instance

1. **Navigate to Azure Portal**:
   - Go to the [Azure Portal](https://portal.azure.com/).

2. **Create a Resource**:
   - Select **Create a Resource** from the Azure portal dashboard.
   - Search for **Azure OpenAI** in the marketplace.

3. **Set Up the Azure OpenAI Resource**:
   - Choose the **East US** region for deployment to ensure capacity for GPT-4 and DALL-E 3 models.
   - Fill in the required details:
     - Resource group: Use an existing one or create a new group.
     - Pricing tier: Select `Standard`.

4. **Deploy the Resource**:
   - Click **Review + Create** and then **Create** to deploy the Azure OpenAI service.

---

### Task 2: Deploy the GPT-4 and DALL-E 3 Models

1. **Access the Azure OpenAI Resource**:
   - Navigate to the Azure OpenAI resource you just created.

2. **Deploy GPT-4**:
   - Go to the **Model Deployments** section and click **Add Deployment**.
   - Choose **GPT-4** as the model and provide a deployment name (e.g., `gpt-4`).
   - Set the deployment configuration as required and deploy the model.

3. **Deploy DALL-E 3**:
   - Repeat the same process to deploy **DALL-E 3**.
   - Use a descriptive deployment name (e.g., `dall-e-3`).

4. **Note Configuration Details**:
   - Once deployed, note down the following details for each model:
     - Deployment Name
     - Endpoint URL

---

### Task 3: Retrieve and Configure API Keys

1. **Get API Keys**:
   - Go to the **Keys and Endpoints** section of your Azure OpenAI resource.
   - Copy the **API Key (API key 1)** and **Endpoint URL**.

2. **Base64 Encode the API Key**:
   - Use the following command to Base64 encode your API key:
     ```bash
     echo -n "<your-api-key>" | base64
     ```
   - Replace `<your-api-key>` with your actual API key.

---

### Task 4: Update AI Service Deployment Configuration in the `Deployment Files` folder.
1. **Modify Secretes YAML**:
   - Edit the `secrets.yaml` file.
   - Replace `OPENAI_API_KEY` placeholder with the Base64-encoded value of the `API_KEY`. 
2. **Modify Deployment YAML**:
   - Edit the `aps-all-in-one.yaml` file.
   - Replace the placeholders with the configurations you retrieved:
     - `AZURE_OPENAI_DEPLOYMENT_NAME`: Enter the deployment name for GPT-4.
     - `AZURE_OPENAI_ENDPOINT`: Enter the endpoint URL for the GPT-4 deployment.
     - `AZURE_OPENAI_DALLE_ENDPOINT`: Enter the endpoint URL for the DALL-E 3 deployment.
     - `AZURE_OPENAI_DALLE_DEPLOYMENT_NAME`: Enter the deployment name for DALL-E 3.

   Example configuration in the YAML file:
   ```yaml
   - name: AZURE_OPENAI_API_VERSION
     value: "2024-07-01-preview"
   - name: AZURE_OPENAI_DEPLOYMENT_NAME
     value: "gpt-4-deployment"
   - name: AZURE_OPENAI_ENDPOINT
     value: "https://<your-openai-resource-name>.openai.azure.com/"
   - name: AZURE_OPENAI_DALLE_ENDPOINT
     value: "https://<your-openai-resource-name>.openai.azure.com/"
   - name: AZURE_OPENAI_DALLE_DEPLOYMENT_NAME
     value: "dalle-3-deployment"
   ```

## Step 4: Deploy the ConfigMaps and Secrets
- Deploy the ConfigMap for RabbitMQ Plugins:
   ```bash
   kubectl apply -f config-maps.yaml
   ```
- Create and Deploy the Secret for OpenAI API:  
   - Make sure that you have replaced Base64-encoded-API-KEY in secrets.yaml with your Base64-encoded OpenAI API key.
   ```bash
   kubectl apply -f secrets.yaml
   ```
- Verify:
   ```bash
   kubectl get configmaps
   kubectl get secrets
   ```

## Step 5: Deploy the Application
   ```bash
   kubectl apply -f store-front-deployment.yaml
   kubectl apply -f store-admin-deployment.yaml
   kubectl apply -f order-service-deployment.yaml
   kubectl apply -f product-service-deployment.yaml
   kubectl apply -f makeline-service-deployment.yaml
   kubectl apply -f ai-service-deployment.yaml
   ```
**Or we can execute keep all deployment files in single file and run below command**
   ```bash
   kubectl apply -f aps-all-in-one.yaml
   ```

### Validate the Deployment
- Check Pods and Services:
   ```bash
   kubectl get pods
   kubectl get services
   ```
- Test Frontend Access:
   - Locate the external IPs for store-front and store-admin services:
   ```bash
   kubectl get services
   ```
   - Access the Store Front app at the external IP on port 80.
   - Access the Store Admin app at the external IP on port 80.
## Step 6: Deploy Virtual Customer and Worker
   ```bash
   kubectl apply -f admin-tasks.yaml
   ```
- Monitor Virtual Customer:
   ```bash
   kubectl logs -f deployment/virtual-customer
   ```
- Monitor Virtual Worker:
   ```bash
   kubectl logs -f deployment/virtual-worker
   ```

## Step 7: Scale and Monitor Services
### Scale Deployments:
- Scale the `order-service` to 3 replicas:
```bash
kubectl scale deployment order-service --replicas=3
```
- Check Scaling:
```bash
kubectl get pods
```
- Monitor Resource Usage:

   - Enable metrics server for resource monitoring.
   - Use kubectl top to monitor pod and node usage:
   ```bash
   kubectl top pods
   kubectl top nodes
   ```

## Step 8: Explore Advanced Features
### AI-Generated Descriptions and Images:
- Use the AI Service for generating product descriptions and images.
- Ensure your OpenAI API key is correctly configured in the deployed secret.
### RabbitMQ Management:
- Access the RabbitMQ management UI:
   ```bash
   kubectl port-forward service/rabbitmq 15672:15672
   ```
   The kubectl port-forward command is used to forward a local port to a port on a Kubernetes resource (e.g., a Pod or Service). This allows you to access the application running in the cluster from your local machine without exposing it externally.


- Login with the default credentials (`username`/`password`).

### MongoDB Shell Access and Database Exploration
In this section, you will use the MongoDB shell to interact with the `orderdb` database, which stores order information for BestBuy. Follow the steps below to connect to the MongoDB pod and explore its contents.

#### **1- Access the MongoDB Shell**
Run the following command to connect to the MongoDB shell inside the running MongoDB pod:
```bash
kubectl exec -it <mongodb-pod-name> -- mongo
```
Explanation: This command uses kubectl exec to open an interactive shell (-it) inside the MongoDB pod and starts the MongoDB shell program (mongo).

#### **2- List All Databases**
Once inside the MongoDB shell, run:
```bash
show dbs
```
Explanation: The show dbs command lists all databases available on the MongoDB server. You should see a list that includes the orderdb, which stores order-related data for the application.
#### **3- Switch to the Order Database**
```bash
use orderdb
```
Explanation: The use orderdb command selects the orderdb database, making it the active database for subsequent queries and commands.
#### **4- List Collections in the Database**
Display all collections in the orderdb database:
```bash
show collections
```
Explanation: The show collections command lists all collections (similar to tables in relational databases) in the current database. The orders collection contains the order data.
#### **5- Query the Orders Collection**
Retrieve all documents in the orders collection:
```bash
db.orders.find()
```
Explanation: The db.orders.find() command fetches and displays all documents (records) in the orders collection. This allows you to view the stored order data, including details such as customer information, products, and order status.

#### By following these steps, you will:
- Connect to the MongoDB shell in the Kubernetes pod.
- Explore the databases and collections used by the application.
- Query the orders collection to examine the data structure and stored records.

## Lab Tasks: Build, Push, and Deploy Docker Images



# CI/CD Pipeline: Full-stack Cloud-native Development: Automating the BestBuy with GitHub Actions

## Prerequisites
1. A GitHub account with access to the forked Algonquin Pet Store repositories from the last lab.
2. Docker Hub account credentials.
3. Access to an AKS cluster as configured in previous labs (You will need to create one for this lab as well).
---
## Introduction to GitHub Actions
**GitHub Actions** is a powerful workflow automation tool integrated directly into GitHub. It enables developers to automate tasks like testing, building, and deploying code using YAML-defined workflows. Workflows consist of jobs, steps, and triggers that execute tasks in response to events, such as a code push or pull request.
### Key Components of a Workflow

- **Workflow**: The pipeline definition written in YAML and stored in the `.github/workflows/` directory.
- **Event**: The trigger for the workflow (e.g., a push to the `main` branch).
- **Job**: A group of steps executed in sequence. Jobs can run independently or depend on other jobs.
- **Step**: A single task, such as running a command or action.
- **Runner**: The virtual machine where jobs run (e.g., `ubuntu-latest`).

---
## Step 1: Fork the Repository and Set Up Secrets
1. **Fork the Required Repositories**
   - Fork the following repositories into your GitHub account:
     - [`store-front`](https://github.com/Fattehali/store-front-bestbuy.git)
     - [`store-admin`](https://github.com/Fattehali/store-admin-bestbuy.git)
     - [`order-service`](https://github.com/Fattehali/order-service-bestbuy.git)
     - [`product-service`](https://github.com/Fattehali/product-service-bestbuy.git)
     - [`makeline-service`](https://github.com/Fattehali/makeline-service-bestbuy.git)
     - [`ai-service`](https://github.com/Fattehali/ai-service-bestbuy.git)

2. **Set Up Secrets**
   - Go to **Settings > Secrets and variables > Actions** in each forked repository.
   - Add the following repository secrets:
     - `DOCKER_USERNAME`: Your Docker Hub username.
     - `DOCKER_PASSWORD`: Your Docker Hub password.
     - `KUBE_CONFIG_DATA`: Base64-encoded content of your Kubernetes configuration file (`kubeconfig`). This is used for authentication with your Kubernetes cluster.
       - Run the following commands to get `KUBE_CONFIG_DATA` after connecting to your AKS cluster:
     ```bash
     cat ~/.kube/config | base64 -w 0 > kube_config_base64.txt
     ``` 
     Use the content of this file as the value for the KUBE_CONFIG_DATA secret in GitHub.

3. **Set Up Environment Variables (Repository Variables)**
   - Go to **Settings > Secrets and variables > Actions** in each forked repository.
   - Add the following repository variables:
     - `DOCKER_IMAGE_NAME`: The name of the Docker image to be built, tagged, and pushed (For example: `store-front-l9`).
     - `DEPLOYMENT_NAME`: The name of the Kubernetes deployment to update (For example: `store-front`).
     - `CONTAINER_NAME`: The name of the container within the Kubernetes deployment to update (For example: `store-front`).
     
---

## Step 2: Create the Workflow File
1. **Create the Workflow Directory**:
   - In each forked repository, create a directory named `.github/workflows/`.

2. **Add the Workflow File**:
   - Copy the `ci_cd.yaml` file from the `Workflow Files` folder into the `.github/workflows/` directory of each forked repository.
---
## Step 3: Understand the Workflow Structure

- The `ci_cd .yaml` file defines a **GitHub Actions workflow** that automates the CI/CD pipeline for your application.

### Key Workflow Steps

1. **Trigger**:
   - The workflow is triggered whenever code is pushed to the `main` branch.
   ```yaml
   on:
     push:
       branches:
         - main
   ```
2. **Jobs**:

   - **Build**: Builds and pushes a Docker image for the service to Docker Hub.
   - **Test**: Runs unit and integration tests.
   - **Release**: Promotes the Docker image to the latest tag.
   - **Deploy**: Deploys the application to AKS.

3. **Secrets and Environment Variables**:
   - Environment Variables and secrets are **securely** injected into the workflow.

---
## Step 4: Run the Workflow
1. **Create AKS cluster**:
   - Create AKS cluster and deploy the Algonquin Pet Store using the provided deployment file in Deployment Filres folder. 

2. **Push Code Changes**:
   - Push a change to the main branch of your repository to trigger the workflow.
3. **Monitor Workflow Execution**:
   - Go to the Actions tab in the repository and verify the progress of the workflow jobs (Build, Test, Release, Deploy).
4. **Validate Deployment**:
   - Use kubectl commands to check the deployment status:
`kubectl get pods` and `kubectl get services`

- **Any issues or limitations in the implementation (Optional)**

<img width="1440" alt="Screenshot 2024-12-12 at 2 35 35 AM" src="https://github.com/user-attachments/assets/e2230382-a8a8-48e4-9b4c-1b338afaf960" />
<img width="1440" alt="Screenshot 2024-12-10 at 1 10 14 PM" src="https://github.com/user-attachments/assets/82f1c26e-f291-407d-972c-420bda15a613" />

  
