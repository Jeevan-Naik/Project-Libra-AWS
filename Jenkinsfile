pipeline {
    agent any

    environment {
        IMAGE_NAME = "project-libra"
        TAG = "uat"
        CONTAINER_NAME = "project-libra-container"
    }

    stages {
        stage('Checkout GitHub Repo') {
            steps {
                // Public repo → no credentials needed
                git branch: 'main', url: 'https://github.com/Jeevan-Naik/Project-Libra-AWS.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t ${IMAGE_NAME}:${TAG} ."
            }
        }

        stage('Run Container') {
            steps {
                bat """
                docker stop ${CONTAINER_NAME} || echo No old container
                docker rm ${CONTAINER_NAME} || echo No old container
                docker run -d --name ${CONTAINER_NAME} -p 8080:80 ${IMAGE_NAME}:${TAG}
                """
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', 
                                                 usernameVariable: 'DOCKERHUB_USER', 
                                                 passwordVariable: 'DOCKERHUB_PASS')]) {
                    bat """
                    echo %DOCKERHUB_PASS% | docker login -u %DOCKERHUB_USER% --password-stdin
                    docker tag ${IMAGE_NAME}:${TAG} %DOCKERHUB_USER%/${IMAGE_NAME}:${TAG}
                    docker push %DOCKERHUB_USER%/${IMAGE_NAME}:${TAG}
                    """
                }
            }
        }
    }

    post {
        always {
            echo "Cleaning up local Docker containers/images"
            bat """
            docker stop ${CONTAINER_NAME} || echo No container
            docker rm ${CONTAINER_NAME} || echo No container
            docker rmi ${IMAGE_NAME}:${TAG} || echo No image
            """
        }
    }
}
