pipeline {
    agent any

    environment {
        PATH = "/usr/local/bin:$PATH"
        DOCKER_IMAGE = "manoz3896/devops-nodejs-app:${BUILD_NUMBER}"
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/MAXBASH/devops-nodejs-app.git'
            }
        }

        stage('Check Docker') {
            steps {
                sh 'which docker'
                sh 'docker --version'
                sh 'docker ps'
            }
        }

        stage('Build') {
            steps {
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
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push $DOCKER_IMAGE'
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker run -d -p 3000:3000 --name devops-nodejs-app $DOCKER_IMAGE'
            }
        }
    }

    post {
        always {
            sh 'docker system prune -f'
        }
    }
}