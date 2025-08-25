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

        stage('Run Unit Tests') {
            steps {
                bat 'echo "Running tests (replace with pytest if needed)"'
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub',
                                                 usernameVariable: 'DOCKERHUB_USER',
                                                 passwordVariable: 'DOCKERHUB_PASS')]) {
                    bat '''
                        echo %DOCKERHUB_PASS% | docker login -u %DOCKERHUB_USER% --password-stdin
                        docker tag project-libra:uat %DOCKERHUB_USER%/project-libra:uat
                        docker push %DOCKERHUB_USER%/project-libra:uat
                    '''
                }
            }
        }

        stage('Deploy to UAT') {
            steps {
                bat 'echo "Deploying
