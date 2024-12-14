pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'bakrm/my-node-app:latest'
        DOCKER_CREDENTIALS = 'dockerhub-credentials' // Replace with your Jenkins Docker Hub credentials ID
    }
    stages {
        stage('Checkout Code') {
            steps {
                echo 'Pulling code from GitHub...'
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                script {
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                echo 'Pushing Docker image to Docker Hub...'
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    script {
                        // Login to Docker Hub
                        sh "echo ${DOCKER_PASS} | docker login -u ${DOCKER_USER} --password-stdin"
                        // Push the image
                        sh "docker push ${DOCKER_IMAGE}"
                    }
                }
            }
        }
        stage('Run Docker Container') {
            steps {
                echo 'Running Docker container...'
                script {
                    sh "docker run -d -p 3000:3000 ${DOCKER_IMAGE}"
                }
            }
        }
        stage('Test Application') {
            steps {
                echo 'Testing the application...'
                script {
                    sh "curl http://localhost:3000"
                }
            }
        }
    }
    post {
        success {
            echo 'Pipeline completed successfully and image is pushed to Docker Hub!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}

