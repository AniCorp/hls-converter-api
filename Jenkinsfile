pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build and Push Docker Images') {
            steps {
                script {
                    docker.image('docker/compose:1.29.2').inside("-u 0:0 -v /var/run/docker.sock:/var/run/docker.sock") {
                        sh 'docker-compose build'
                        sh 'docker-compose push'
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    docker.image('docker/compose:1.29.2').inside("-u 0:0 -v /var/run/docker.sock:/var/run/docker.sock") {
                        sh 'docker-compose down'
                        sh 'docker-compose up -d'
                    }
                }
            }
        }
    }
}
