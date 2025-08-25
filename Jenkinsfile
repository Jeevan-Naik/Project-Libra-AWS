pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'jeevannaik1999/project-libra:uat'
        CONTAINER_NAME = 'project-libra-container'
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Checking out from GitHub...'
                git url: 'https://github.com/Jeevan-Naik/Project-Libra-AWS.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                bat "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Run Container') {
            steps {
                echo 'Stopping old container if exists...'
                bat "docker stop ${CONTAINER_NAME} 2>nul || echo No old container"
                bat "docker rm ${CONTAINER_NAME} 2>nul || echo No old container"

                echo 'Starting new container...'
                bat "docker run -d --name ${CONTAINER_NAME} ${DOCKER_IMAGE}"
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    echo 'Logging in to DockerHub...'
                    bat "docker login -u %DOCKER_USER% -p %DOCKER_PASS%"
                    
                    echo 'Pushing Docker image...'
                    bat "docker push ${DOCKER_IMAGE}"
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up local Docker containers/images...'
            bat "docker stop ${CONTAINER_NAME} 2>nul || echo No container"
            bat "docker rm ${CONTAINER_NAME} 2>nul || echo No container"
        }

        success {
            echo 'Pipeline completed successfully! 🎉'
        }

        failure {
            echo 'Pipeline failed! Check logs for details.'
        }
    }
}
