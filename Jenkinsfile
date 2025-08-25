pipeline {
    agent any

    environment {
        CONTAINER_NAME = 'project-libra-container'
        IMAGE_NAME = 'jeevannaik1999/project-libra:uat'
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Checking out from GitHub...'
                git branch: 'main', url: 'https://github.com/Jeevan-Naik/Project-Libra-AWS.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                bat "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Run Container') {
            steps {
                echo 'Stopping old container if exists...'
                bat(script: "docker stop ${CONTAINER_NAME} 2>nul || exit 0", returnStatus: true)
                bat(script: "docker rm ${CONTAINER_NAME} 2>nul || exit 0", returnStatus: true)

                echo 'Running new container...'
                bat "docker run -d --name ${CONTAINER_NAME} ${IMAGE_NAME}"
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    echo 'Logging in to DockerHub...'
                    bat "docker login -u %DOCKER_USER% -p %DOCKER_PASS%"

                    echo 'Pushing Docker image...'
                    bat "docker push ${IMAGE_NAME}"
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up local Docker containers/images...'
            // Stop container if exists, ignore errors
            bat(script: "docker stop ${CONTAINER_NAME} 2>nul || exit 0", returnStatus: true)
            bat(script: "docker rm ${CONTAINER_NAME} 2>nul || exit 0", returnStatus: true)
            bat(script: "docker rmi ${IMAGE_NAME} 2>nul || exit 0", returnStatus: true)
        }

        success {
            echo 'Pipeline completed successfully! 🎉'
        }

        failure {
            echo 'Pipeline failed! Check logs for details.'
        }
    }
}
