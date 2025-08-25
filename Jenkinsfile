pipeline {
    agent any

    environment {
        IMAGE_NAME = "project-libra"
        IMAGE_TAG = "uat"
        CONTAINER_NAME = "project-libra-container"
    }

    stages {
        stage('Checkout GitHub Repo') {
            steps {
                // Public repo, no credentials needed
                git branch: 'main', url: 'https://github.com/Jeevan-Naik/Project-Libra-AWS.git'
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
                // DockerHub credentials in Jenkins: ID = dockerhub
                withCredentials([usernamePassword(credentialsId: 'dockerhub', 
                                                 usernameVariable: 'DOCKERHUB_USER', 
                                                 passwordVariable: 'DOCKERHUB_PASS')]) {
                    bat """
                    echo %DOCKERHUB_PASS% | docker login -u %DOCKERHUB_USER% --password-stdin
                    docker tag ${IMAGE_NAME}:${IMAGE_TAG} %DOCKERHUB_USER%/${IMAGE_NAME}:${IMAGE_TAG}
                    docker push %DOCKERHUB_USER%/${IMAGE_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up local Docker containers/images'
            bat "docker stop ${CONTAINER_NAME} || echo No container"
            bat "docker rm ${CONTAINER_NAME} || echo No container"
            bat "docker rmi ${IMAGE_NAME}:${IMAGE_TAG} || echo No image"
        }
    }
}
