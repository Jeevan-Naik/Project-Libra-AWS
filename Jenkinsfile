pipeline {
    agent any

    environment {
        IMAGE_NAME = "jeevannaik1999/project-libra"
        IMAGE_TAG = "uat"
        CONTAINER_NAME = "project-libra-container"
    }

    stages {
        stage('Checkout SCM') {
            steps {
                echo "Checking out from GitHub..."
                git branch: 'main', url: 'https://github.com/Jeevan-Naik/Project-Libra-AWS.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                bat "docker build -t ${env.IMAGE_NAME}:${env.IMAGE_TAG} ."
            }
        }

        stage('Run Container') {
            steps {
                echo "Stopping old container if exists..."
                bat """
                docker stop ${env.CONTAINER_NAME} 2>nul || echo No old container
                docker rm ${env.CONTAINER_NAME} 2>nul || echo No old container
                docker run -d --name ${env.CONTAINER_NAME} ${env.IMAGE_NAME}:${env.IMAGE_TAG}
                """
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', 
                                                  usernameVariable: 'DOCKER_USER', 
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    echo "Logging in to DockerHub..."
                    bat "docker login -u %DOCKER_USER% -p %DOCKER_PASS%"
                    echo "Pushing Docker image..."
                    bat "docker push ${env.IMAGE_NAME}:${env.IMAGE_TAG}"
                }
            }
        }
    }

    post {
        always {
            echo "Cleaning up local Docker containers/images..."
            bat """
            docker stop ${env.CONTAINER_NAME} 2>nul || echo No container
            docker rm ${env.CONTAINER_NAME} 2>nul || echo No container
            docker rmi ${env.IMAGE_NAME}:${env.IMAGE_TAG} 2>nul || echo No image
            """
        }

        success {
            echo "Pipeline completed successfully!"
        }

        failure {
            echo "Pipeline failed. Check logs for details."
        }
    }
}
