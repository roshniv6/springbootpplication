pipeline {
    agent any
tools {
        maven 'maven-3.8.8' 
    }
    environment {
        DOCKER_IMAGE = "roshniaishu6/springbootpplication:latest"
        DOCKER_REGISTRY_CREDENTIALS_ID = 'docker-registry-credentials'
        GIT_CREDENTIALS_ID = 'github-credentials'
        DOCKER_TAG = "latest"
    }

    stages {
        stage('Clone Repository') {
            steps {
              git branch: 'main', credentialsId: "${GIT_CREDENTIALS_ID}", url: 'https://github.com/roshniv6/springbootpplication.git'
            }
        }

        stage('Maven Build') {
            steps {
                script {
                    sh 'mvn clean package'
                }
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG}-${env.BUILD_NUMBER} ."
                }
            }
        }

        stage('Docker Push') {
            steps {
                script {
                    docker.withRegistry('', DOCKER_REGISTRY_CREDENTIALS_ID) {
                        sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}-${env.BUILD_NUMBER}"
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Docker image built and pushed successfully!'
        }
        failure {
            echo 'Build or push failed!'
        }
    }
}
