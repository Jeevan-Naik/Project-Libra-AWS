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

        stage('Run Unit Tests') {
            steps {
                bat 'echo "Running tests (add pytest here if needed)"'
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'DOCKERHUB_PASS')]) {
                    bat """
                    REM Login to DockerHub securely using PAT
                    echo %DOCKERHUB_PASS% | docker login --username jeevannaik1999 --password-stdin

                    REM Tag the Docker image
                    docker tag %DOCKER_IMAGE% jeevannaik1999/%DOCKER_IMAGE%

                    REM Push the image
                    docker push jeevannaik1999/%DOCKER_IMAGE%
                    """
                }
            }
        }

        stage('Deploy to UAT') {
            steps {
                bat 'docker-compose -f docker-compose.uat.yml up -d --build'
            }
        }
    }
}