pipeline {
    agent any

    environment {
        DOCKER_HUB = "nguyenbill"
    }

    stages {

        stage('Build Image') {
            steps {
                sh 'docker build -t $DOCKER_HUB/fastapi-app:latest -f Docker/app/Dockerfile .'
            }
        }

        stage('Push Image') {
            steps {
                script {
                    docker.withRegistry('', 'dockerhub-creds') {
                        sh 'docker push $DOCKER_HUB/fastapi-app:latest'
                    }
                }
            }
        }
    }
}