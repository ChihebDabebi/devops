pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'chiheb634/springboot-app'
    }

    stages {
        stage('Build & Test') {
            steps {
                sh "mvn clean"
                sh "mvn compile"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-credentials', url: '']) {
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }

        stage('Deploy on Vagrant') {
            steps {
                sh 'docker pull $DOCKER_IMAGE'
                sh 'docker run -d -p 8080:8080 $DOCKER_IMAGE'
            }
        }
    }
}

