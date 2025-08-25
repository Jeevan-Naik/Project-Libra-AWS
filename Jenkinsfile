pipeline {
    agent any

    environment {
        IMAGE_NAME = "project-libra"
        IMAGE_TAG = "uat"
        CONTAINER_NAME = "project-libra-container"
        DOCKERHUB_CRED = credentials('dockerhub') // Optional if pushing to DockerHub
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Run Container') {
            steps {
                // Stop and remove old container if exists
                bat "docker stop ${CONTAINER_NAME} || echo No old container"
                bat "docker rm ${CONTAINER_NAME} || echo No old container"
                
                // Run new container
                bat "docker run -d --name ${CONTAINER_NAME} -p 8080:80 ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat "echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin"
                    bat "docker tag ${IMAGE_NAME}:${IMAGE_TAG} %DOCKER_USER%/${IMAGE_NAME}:${IMAGE_TAG}"
                    bat "docker push %DOCKER_USER%/${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }
    }

    post {
        always {
            echo "Cleaning up local Docker containers/images"
            // Ignore errors to prevent pipeline failure
            bat "docker stop ${CONTAINER_NAME} || echo No container"
            bat "docker rm ${CONTAINER_NAME} || echo No container"
            bat "docker rmi ${IMAGE_NAME}:${IMAGE_TAG} || echo No image"
        }
    }
}
