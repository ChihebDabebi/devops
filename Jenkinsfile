pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'chiheb634/springboot-app'
        SONARQUBE_SERVER = 'http://localhost:9000'
        SONARQUBE_TOKEN = credentials('jenkins-sonar') // Assuming you have stored the token in Jenkins credentials
        NEXUS_REPO_URL = 'http://localhost:8081/repository/maven-releases/'  // Change to your Nexus URL
    }

    stages {
        stage('Build & Test') {
            steps {
                sh "mvn clean"
                sh "mvn compile"
                sh "mvn package"
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'jenkins-sonar', variable: 'SONARQUBE_TOKEN')]) {
                        sh "mvn sonar:sonar -Dsonar.login=$SONARQUBE_TOKEN -Dsonar.host.url=$SONARQUBE_SERVER"
                    }
                }
            }
        }

        stage('Deploy to Nexus') {
            steps {
                sh 'mvn deploy -Dmaven.test.skip=true'
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
                sh 'docker run -d -p 5000:8082 $DOCKER_IMAGE'
            }
        }
    }
}
