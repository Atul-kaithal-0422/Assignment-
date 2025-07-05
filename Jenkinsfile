pipeline {
    agent any

    environment {
        IMAGE_NAME = 'atulkaithal0422/nginx-custom'
        CONTAINER_NAME = 'nginx-container'
        DOCKERHUB_CREDENTIALS_ID = 'dockerhub-creds'  // Jenkins credentials ID
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/Atul-kaithal-0422/Assignment-.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %IMAGE_NAME% ."
            }
        }

        stage('Run Docker Container') {
            steps {
               bat "docker run -d --name %CONTAINER_NAME% -p 8090:80 %IMAGE_NAME%"
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: env.DOCKERHUB_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                bat "docker push %IMAGE_NAME%"
            }
        }

        stage('Run Tests') {
            steps {
               bat """
timeout /t 5
curl http://localhost:8090 || exit 1
"""

            }
        }

        stage('Deploy / Success Message') {
            steps {
                echo '✅ Deployment successful! Docker image pushed and container tested.'
            }
        }
    }

    post {
        always {
            echo '🧹 Cleaning up...'
            bat """
            docker stop %CONTAINER_NAME% || exit 0
            docker rm %CONTAINER_NAME% || exit 0
            """
        }
    }
}
