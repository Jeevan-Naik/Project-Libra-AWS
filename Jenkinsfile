pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "project-libra:uat"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/Jeevan-Naik/Project-Libra-AWS.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %DOCKER_IMAGE% ."
            }
        }

        stage('Run Container') {
            steps {
                bat "docker rm -f project-libra-container || echo No old container"
                bat "docker run -d --name project-libra-container -p 8080:80 %DOCKER_IMAGE%"
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub',
                                                  usernameVariable: 'DOCKERHUB_USER',
                                                  passwordVariable: 'DOCKERHUB_PASS')]) {
                    bat "echo %DOCKERHUB_PASS% | docker login -u %DOCKERHUB_USER% --password-stdin"
                    bat "docker tag project-libra:uat %DOCKERHUB_USER%/project-libra:uat"
                    bat "docker push %DOCKERHUB_USER%/project-libra:uat"
                }
            }
        }

        stage('Deploy') {
            steps {
                bat "echo Deploying container..."
            }
        }
    }
}
