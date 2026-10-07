pipeline {
    agent any
    tools {
        maven 'maven-3.8.8' 
    }
    environment {
        DOCKER_IMAGE = "roshniaishu6/springbootpplication"
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
                    // 1. Download official static Docker CLI binary if it doesn't exist in workspace
                    sh '''
                        if [ ! -f ./docker/docker ]; then
                            echo "Downloading static Docker CLI binary..."
                            curl -fsSL https://docker.com -o docker.tgz
                            tar -xzvf docker.tgz docker/docker
                            rm docker.tgz
                        fi
                    '''
                    
                    // 2. Add the downloaded binary path to the environment PATH variable
                    withEnv(["PATH+DOCKER=${WORKSPACE}/docker"]) {
                        sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG}-${env.BUILD_NUMBER} ."
                    }
                }
            }
        }

        stage('Docker Push') {
            steps {
                script {
                    // Use the same binary path wrapper to authorize and push
                    withEnv(["PATH+DOCKER=${WORKSPACE}/docker"]) {
                        docker.withRegistry('', DOCKER_REGISTRY_CREDENTIALS_ID) {
                            sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}-${env.BUILD_NUMBER}"
                        }
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
