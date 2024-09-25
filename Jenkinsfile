pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "manoz3896/devops-nodejs-app:${BUILD_NUMBER}"
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    }

    stages {
        stage('Diagnostic') {

        stage('Build') {
            steps {
                sh '$DOCKER build -t $DOCKER_IMAGE .'
            }
        }

        stage('Test') {
            steps {
                sh '$DOCKER run --rm $DOCKER_IMAGE npm test'
            }
        }

        stage('Push to Registry') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | $DOCKER login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh '$DOCKER push $DOCKER_IMAGE'
            }
        }

        stage('Deploy') {
            steps {
                sh '$DOCKER run -d -p 3000:3000 --name devops-nodejs-app $DOCKER_IMAGE'
            }
        }
    }

    post {
        always {
            sh '$DOCKER system prune -f'
        }
    }
}