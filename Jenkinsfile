pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS_ID = 'dockerhub-credentials'
        DOCKER_IMAGE_NAME         = 'ayoubdev29/backend-service'
    }

    stages {
        stage('1. Checkout') {
            steps {
                checkout scm
            }
        }

        stage('2. Build Docker Image') {
            steps {
                script {
                    // We will build the image and tag it with the unique Git commit hash.
                    // The docker.build command returns an object representing the image.
                    def builtImage = docker.build("${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}", ".")

                    // Now, add the 'latest' tag to the image we just built.
                    // The .tag() method only needs the new tag name.
                    builtImage.tag("latest")
                }
            }
        }

        stage('3. Push to Docker Hub') {
            steps {
                script {
                    // Use the 'dockerhub-credentials' we stored in Jenkins to log in
                    docker.withRegistry('https://registry.hub.docker.com', DOCKER_HUB_CREDENTIALS_ID) {

                        // Push the tag with the build number
                        docker.image("${DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}").push()

                        // Push the 'latest' tag
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