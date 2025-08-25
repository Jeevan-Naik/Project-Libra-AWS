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
                bat 'docker build -t %DOCKER_IMAGE% .'
            }
        }

        stage('Run Container') {
            steps {
                bat '''
                    docker stop project-libra-container || true
                    docker rm project-libra-container || true
                    docker run -d --name project-libra-container -p 8080:80 %DOCKER_IMAGE%
                '''
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials',
                                                 usernameVariable: 'DOCKERHUB_USER',
                                                 passwordVariable: 'DOCKERHUB_PASS')]) {
                    bat """
                        echo %DOCKERHUB_PASS% | docker login -u %DOCKERHUB_USER% --password-stdin
                        docker tag project-libra:uat %DOCKERHUB_USER%/project-libra:uat
                        docker push %DOCKERHUB_USER%/project-libra:uat
                    """
                }
            }
        }

        stage('Deploy') {
            steps {
                bat 'echo Deploying container...'
            }
        }
    }
}
