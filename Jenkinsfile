pipeline {
    agent any
    tools {
        maven 'maven-3.8.8' 
    }
    environment {
        // FIXED: Removed ':latest' from the image name to prevent the double-colon error
        DOCKER_IMAGE = "roshniaishu6/springbootpplication"
        DOCKER_REGISTRY_CREDENTIALS_ID = 'docker-registry-credentials'
        GIT_CREDENTIALS_ID = 'github-credentials'
        DOCKER_TAG = "latest"
    }

    stages {
        stage('Check Environment') {
            steps {
                script {
                    echo "=== Checking available commands ==="
                    // This prints the current user running the job
                    sh 'whoami'
                    
                    // This tests if 'docker' exists in the current system path
                    try {
                        sh 'docker --version'
                    } catch (Exception e) {
                        echo "Diagnostic Result: The 'docker' command is definitely NOT installed or accessible in this environment path yet."
                    }
                }
            }
        }
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
                    // This now correctly evaluates to: roshniaishu6/springbootpplication:latest-2
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
