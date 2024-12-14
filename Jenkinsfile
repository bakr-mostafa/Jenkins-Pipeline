pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'bakrm/my-node-app:latest'
        DOCKER_CREDENTIALS = 'dockerhub-credentials' // Replace with your Jenkins credentials ID
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
                    sh '''
                    docker build -t ${DOCKER_IMAGE} .
                    '''
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo 'Pushing Docker image to Docker Hub...'
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    script {
                        // Secure Docker login and push
                        sh '''
                        echo "${DOCKER_PASS}" | docker login -u "${DOCKER_USER}" --password-stdin
                        docker push ${DOCKER_IMAGE}
                        '''
                    }
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                echo 'Running Docker container...'
                script {
                    sh '''
                    docker run -d -p 3000:3000 --name my-node-app ${DOCKER_IMAGE}
                    '''
                }
            }
        }

        stage('Test Application') {
            steps {
                echo 'Testing the application...'
                script {
                    sh '''
                    sleep 5 # Wait for the container to start
                    curl --retry 5 --retry-delay 2 http://localhost:3000
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully! Docker image has been pushed and container is running.'
        }
        failure {
            echo 'Pipeline failed! Check the logs for details.'
        }
        always {
            echo 'Cleaning up...'
            script {
                sh '''
                docker stop my-node-app || true
                docker rm my-node-app || true
                '''
            }
        }
    }
}

