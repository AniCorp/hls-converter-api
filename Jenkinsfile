pipeline {
    agent any

    environment {
        API_IMAGE_NAME     = 'dawoodmasood/hls-converter-api'
        ENCODER_IMAGE_NAME = 'dawoodmasood/hls-converter-encoder'
        IMAGE_TAG          = 'latest'
        API_PORT           = 9997
        ENCODER_PORT       = 9996
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build API Image') {
            steps {
                script {
                    // Build the API image using Dockerfile.api
                    apiImage = docker.build("${API_IMAGE_NAME}:${IMAGE_TAG}", "-f api.dockerfile .")
                }
            }
        }
        stage('Build Encoder Image') {
            steps {
                script {
                    // Build the Encoder image using Dockerfile.encoder
                    encoderImage = docker.build("${ENCODER_IMAGE_NAME}:${IMAGE_TAG}", "-f encoder.dockerfile .")
                }
            }
        }
        stage('Deploy API') {
            steps {
                script {
                    // Check for an already running container on the API port
                    def runningApi = sh(script: "docker ps -q --filter publish=${API_PORT}", returnStdout: true).trim()
                    if (runningApi) {
                        sh "docker stop ${runningApi}"
                        sh "docker rm ${runningApi}"
                    }
                    
                    // Deploy the API container
                    sh "docker run -d --name hls-converter-api -p ${API_PORT}:${API_PORT} ${API_IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }
        stage('Deploy Encoder') {
            steps {
                script {
                    // Check for an already running container on the Encoder port
                    def runningEncoder = sh(script: "docker ps -q --filter publish=${ENCODER_PORT}", returnStdout: true).trim()
                    if (runningEncoder) {
                        sh "docker stop ${runningEncoder}"
                        sh "docker rm ${runningEncoder}"
                    }
                    
                    // Deploy the Encoder container
                    sh "docker run -d --name hls-converter-encoder -p ${ENCODER_PORT}:${ENCODER_PORT} ${ENCODER_IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
