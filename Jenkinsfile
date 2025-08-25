pipeline {
    agent any

    environment {
        IMAGE_NAME = "project-libra"
        IMAGE_TAG  = "uat"
        CONTAINER_NAME = "project-libra-container"
        DOCKERHUB_CRED = credentials('dockerhub-cred') // Jenkins credentials ID
    }

    stages {

        stage('Checkout SCM') {
            steps {
                // Checkout your public GitHub repo
                git url: 'https://github.com/Jeevan-Naik/Project-Libra-AWS.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Run Container') {
            steps {
                bat """
                docker stop ${CONTAINER_NAME} 2>nul || echo No old container
                docker rm ${CONTAINER_NAME} 2>nul || echo No old container
                docker run -d --name ${CONTAINER_NAME} -p 8080:80 ${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', 
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
            echo "Cleaning up local Docker containers/images"
            bat """
            docker stop ${CONTAINER_NAME} 2>nul || echo No container
            docker rm ${CONTAINER_NAME} 2>nul || echo No container
            docker rmi ${IMAGE_NAME}:${IMAGE_TAG} 2>nul || echo No image
            """
        }
    }
}
