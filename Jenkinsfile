pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS_ID = 'dockerhub-credentials'
        DOCKER_IMAGE_NAME         = 'ayoubdev29/backend-service'
        KUBECONFIG_CREDENTIAL_ID  = 'kubeconfig-aws'
    }

    stages {
        stage('1. Checkout') { ... } // (Keep stage 1 as is)
        stage('2. Build Docker Image') { ... } // (Keep stage 2 as is)
        stage('3. Push to Docker Hub') { ... } // (Keep stage 3 as is)

        stage('4. Deploy to Kubernetes') {
            steps {
                // Use the kubeconfig file we stored as a secret in Jenkins
                withKubeconfig([credentialsId: KUBECONFIG_CREDENTIAL_ID]) {
                    script {
                        echo "Applying Kubernetes manifests..."
                        // Apply the deployment and service files
                        sh "kubectl apply -f k8s/"

                        echo "Updating deployment with new image..."
                        // Tell the deployment to use the new image we just pushed
                        sh "kubectl set image deployment/backend-service-deployment backend-service-container=${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}"

                        echo "Waiting for rollout to complete..."
                        sh "kubectl rollout status deployment/backend-service-deployment"
                    }
                }
            }
        }
    }
}