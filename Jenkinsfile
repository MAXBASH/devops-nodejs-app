pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "manoz3896/devops-nodejs-app:${BUILD_NUMBER}"
        // Use credentials to inject Docker Hub username and password
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/MAXBASH/devops-nodejs-app.git'
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'docker:latest'
                    args '-v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                sh 'docker version'
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Test') {
            steps {
                sh 'docker run --rm $DOCKER_IMAGE npm test'
            }
        }

        stage('Push to Registry') {
            steps {
                // Login to Docker Hub using credentials
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'

                // Push Docker image
                sh 'docker push $DOCKER_IMAGE'
            }
        }

        stage('Deploy') {
            steps {
                // Deploy the Docker container
                sh 'docker run -d -p 3000:3000 --name devops-nodejs-app $DOCKER_IMAGE'
            }
        }
    }

    post {
        always {
            script {
                try {
                    // Clean up Docker images and containers to save space
                    sh 'docker system prune -f'
                } catch (err) {
                    echo "Docker prune failed: ${err}"
                }
            }
        }
    }
}