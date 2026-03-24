pipeline {
    agent any

    environment {
        DOCKER_HUB = "nguyenbill"
    }

    stages {

        stage('Build Image') {
            steps {
                sh 'docker build -t $DOCKER_HUB/fastapi-app:latest .'
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $DOCKER_HUB/fastapi-app:latest'
            }
        }
    }
}