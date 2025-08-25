pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "project-libra:uat"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/Jeevan-Naik/Project-Libra-AWS.git'
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
                docker stop project-libra-container || echo No old container
                docker rm project-libra-container || echo No old container
                docker run -d --name project-libra-container -p 8080:80 %DOCKER_IMAGE%
                '''
            }
        }

        stage('Push to DockerHub') {
            steps {
                // No login needed for public repo
                bat '''
                docker tag %DOCKER_IMAGE% jeevannaik1999/project-libra:uat
                docker push jeevannaik1999/project-libra:uat
                '''
            }
        }

        stage('Deploy') {
            steps {
                bat 'echo Deploying container...'
            }
        }
    }
}
