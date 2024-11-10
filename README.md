To set up an end-to-end CI/CD pipeline using Jenkins, the stages will include the following:

1. **Clone the repository**: Use Git to fetch the source code.
2. **Build the JAR file**: Use Maven to compile and package the application into a JAR file.
3. **Build the Docker image**: Build a Docker image using the `Dockerfile` in your repository.
4. **Push the Docker image to Docker Hub**: Push the image to your Docker Hub repository.
5. **Deploy to AKS (Azure Kubernetes Service)**: Deploy the Docker container to an AKS cluster.

I'll walk you through the steps to create a Jenkins pipeline using a `Jenkinsfile` for this process. The pipeline will run on a Jenkins agent, perform the above actions, and deploy your app to AKS.

### Prerequisites

1. **Jenkins installed** (either locally or on a server, with appropriate agents set up).
2. **Docker** installed on the Jenkins server or agents.
3. **Docker Hub account** to push images.
4. **Azure account and AKS cluster** set up.
5. **GitHub repository** or other Git-based repository with the necessary source code.

### Step-by-Step Jenkins Pipeline Setup

1. **Create a Jenkins Pipeline (`Jenkinsfile`)**

In the root of your repository, create a `Jenkinsfile` to define your pipeline stages.

Here’s the full pipeline definition using declarative syntax in Jenkins:

```groovy
pipeline {
    agent any  // Jenkins will run this pipeline on any available agent.

    environment {
        DOCKER_HUB_USERNAME = credentials('docker-hub-username')  // Jenkins credentials for Docker Hub username
        DOCKER_HUB_PASSWORD = credentials('docker-hub-password')  // Jenkins credentials for Docker Hub password
        AZURE_CREDENTIALS = credentials('azure-credentials')  // Jenkins credentials for Azure service principal
        IMAGE_NAME = "your-dockerhub-username/your-image-name"
        AKS_CLUSTER_NAME = "your-aks-cluster-name"
        RESOURCE_GROUP = "your-aks-resource-group"
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Checkout the code from Git repository
                    checkout scm
                }
            }
        }

        stage('Build JAR') {
            steps {
                script {
                    // Run Maven to build the JAR file
                    sh 'mvn clean package -DskipTests'  // Adjust to your needs
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    sh 'docker build -t $IMAGE_NAME:latest .'
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    // Login to Docker Hub and push the image
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                        sh "docker push $IMAGE_NAME:latest"
                    }
                }
            }
        }

        stage('Deploy to AKS') {
            steps {
                script {
                    // Login to Azure
                    withCredentials([azureServicePrincipal(credentialsId: 'azure-credentials')]) {
                        sh 'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET --tenant $AZURE_TENANT_ID'
                    }

                    // Configure kubectl to use AKS cluster
                    sh """
                        az aks get-credentials --resource-group $RESOURCE_GROUP --name $AKS_CLUSTER_NAME
                    """

                    // Deploy to AKS
                    sh """
                        kubectl apply -f k8s/deployment.yaml  # Kubernetes deployment file path
                        kubectl set image deployment/your-deployment-name your-container-name=$IMAGE_NAME:latest
                    """
                }
            }
        }
    }

    post {
        always {
            // Clean up Docker images (optional)
            sh 'docker rmi $IMAGE_NAME:latest || true'
        }
        success {
            echo "Pipeline successfully completed!"
        }
        failure {
            echo "Pipeline failed. Please check the logs."
        }
    }
}
```

### Explanation of the Pipeline

#### **Environment Variables**:
- `DOCKER_HUB_USERNAME` and `DOCKER_HUB_PASSWORD` are credentials stored in Jenkins (you need to store them securely under **Jenkins > Manage Jenkins > Manage Credentials**).
- `IMAGE_NAME`: The Docker image name.
- `AKS_CLUSTER_NAME` and `RESOURCE_GROUP`: The name of your Azure Kubernetes Service cluster and resource group.
- `AZURE_CREDENTIALS`: Azure service principal credentials for logging into Azure.

#### **Stages**:

1. **Checkout**: 
   - `checkout scm` checks out the code from your source code repository.

2. **Build JAR**:
   - `sh 'mvn clean package -DskipTests'` runs the Maven build command to package the application into a JAR file. You can adjust the Maven command if needed (e.g., to run tests).

3. **Build Docker Image**:
   - `docker build -t $IMAGE_NAME:latest .` builds the Docker image using the `Dockerfile` present in your repository.

4. **Push Docker Image to Docker Hub**:
   - Logs into Docker Hub using the `docker login` command and pushes the image to Docker Hub using `docker push`.

5. **Deploy to AKS**:
   - Logs into Azure using the service principal credentials.
   - Configures `kubectl` to use your AKS cluster by getting the credentials from Azure.
   - Applies the Kubernetes deployment using the `kubectl apply -f` command and then updates the image of the container in the AKS deployment using `kubectl set image`.

#### **Post Actions**:
- `always` cleans up the Docker images (optional but recommended to save space on the Jenkins agent).
- `success` and `failure` block to print messages based on the result of the pipeline.

### 2. **Setup Jenkins**

#### **Install Necessary Plugins**:
- **Git Plugin**: For cloning the repository.
- **Maven Integration Plugin**: To build Java projects.
- **Docker Pipeline Plugin**: To build and push Docker images.
- **Azure CLI Plugin**: To interact with Azure.
- **Kubernetes CLI Plugin**: To interact with the AKS cluster.

#### **Configure Jenkins Credentials**:
1. **Docker Hub Credentials**: 
   - Go to **Jenkins > Manage Jenkins > Manage Credentials** and add a new **Username/Password** credential for Docker Hub with your Docker Hub username and password.

2. **Azure Service Principal Credentials**:
   - Use the [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/authenticate-azure-cli) to create a service principal for Jenkins to authenticate with Azure.
   - Store the `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`, and `AZURE_TENANT_ID` as Jenkins credentials.

3. **GitHub Credentials (if needed)**:
   - If your repository is private, you can set up GitHub credentials to clone the repo.

#### **Add the `Jenkinsfile` to Your Repository**:
Make sure to add the `Jenkinsfile` to the root of your GitHub (or GitLab/Bitbucket) repository.

### 3. **Configure the Jenkins Pipeline Job**

1. **Create a New Pipeline Job**:
   - Go to **Jenkins > New Item**.
   - Select **Pipeline** and give it a name.
   - In the pipeline section, choose **Pipeline script from SCM**.
   - Select **Git** as the SCM and provide the repository URL.
   - Jenkins will automatically detect the `Jenkinsfile` and run the pipeline based on that.

2. **Run the Pipeline**:
   - Trigger the pipeline manually or set it to run automatically on each push to your Git repository.
   - The pipeline will execute each stage: clone the repo, build the JAR, build the Docker image, push it to Docker Hub, and deploy it to AKS.

### 4. **Testing and Debugging**

- Ensure that each step runs correctly and that the necessary credentials are configured in Jenkins.
- After the pipeline runs, check the console output for any errors or issues, especially in the Docker build, Docker Hub push, or AKS deployment steps.

---

This is a complete, end-to-end Jenkins pipeline for building, pushing, and deploying a Java application to Azure Kubernetes Service (AKS) using Maven, Docker, and Jenkins. You can modify this pipeline based on your specific needs or adjust the stages for testing, deployment strategies, or notifications.
