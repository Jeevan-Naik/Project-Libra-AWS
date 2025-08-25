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
                bat 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Run Unit Tests') {
            steps {
                bat 'echo "Running tests (can add pytest here later)"'
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'DOCKERHUB_PASS')]) {
                    bat '''
                    echo $DOCKERHUB_PASS | docker login -u jeevannaik1999 --password-stdin
                    docker tag $DOCKER_IMAGE jeevannaik1999/$DOCKER_IMAGE
                    docker push jeevannaik1999/$DOCKER_IMAGE
                    '''
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