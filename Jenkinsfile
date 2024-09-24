pipeline {
    agent any

    environment {
        // Define environment variables
        DOCKER_IMAGE = "manoz3896/devops-nodejs-app:${BUILD_NUMBER}"
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    }

    stages {
        stage('Checkout') {
            steps {
                // Clone the repository
                git 'https://github.com/MAXBASH/devops-nodejs-app.git'
            }
        }

        stage('Build') {
            steps {
                script {
                    // Build Docker image
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Run tests inside the Docker container
                    sh 'docker run --rm $DOCKER_IMAGE npm test'
                }
            }
        }

        stage('Push to Registry') {
            steps {
                script {
                    // Login to Docker Hub
                    sh 'echo $DOCKERHUB_PASSWORD | docker login -u $DOCKERHUB_USERNAME --password-stdin'

                    // Push Docker image
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Deploy the Docker container (for testing purposes, run it locally)
                    sh 'docker run -d -p 3000:3000 --name devops-nodejs-app $DOCKER_IMAGE'
                }
            }
        }
    }

    post {
        always {
            // Clean up Docker images and containers to save space
            sh 'docker system prune -f'
        }
    }
}