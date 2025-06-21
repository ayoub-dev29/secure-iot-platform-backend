pipeline {
    // Run this pipeline on any available Jenkins agent
    agent any

    // Environment variables available to all stages
    environment {
        DOCKER_HUB_CREDENTIALS_ID = 'dockerhub-credentials'
        DOCKER_IMAGE_NAME         = 'ayoubdev29/backend-service'
    }

    stages {
        stage('1. Checkout') {
            steps {
                // This step checks out the code from the repository configured in the Jenkins job
                checkout scm
            }
        }

        stage('2. Build Docker Image') {
            steps {
                script {
                    // We will tag our image with the unique Git commit hash for good versioning
                    def commitHash = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
                    docker.build("${DOCKER_IMAGE_NAME}:${commitHash}", ".")
                    // Also tag it as 'latest' for convenience
                    docker.image("${DOCKER_IMAGE_NAME}:${commitHash}").tag("${DOCKER_IMAGE_NAME}:latest")
                }
            }
        }

        stage('3. Push to Docker Hub') {
            steps {
                script {
                    def commitHash = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
                    // Use the 'dockerhub-credentials' we stored in Jenkins to log in and push
                    docker.withRegistry('https://registry.hub.docker.com', DOCKER_HUB_CREDENTIALS_ID) {
                        docker.image("${DOCKER_IMAGE_NAME}:${commitHash}").push()
                        docker.image("${DOCKER_IMAGE_NAME}:latest").push()
                    }
                }
            }
        }

        stage('4. Deploy to Kubernetes') {
            steps {
                // This is our next step! For now, we just print a message.
                echo "Deployment stage is next! For now, we are just building and pushing."
            }
        }
    }
}