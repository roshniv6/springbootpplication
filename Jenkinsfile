pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "roshniaishu6/springbootpplication:latest"
        DOCKER_REGISTRY_CREDENTIALS_ID = 'docker-registry-credentials'
        KUBECONFIG_CREDENTIALS_ID = 'kubeconfig-credentials'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/roshniv6/springbootpplication.git'
            }
        }

        stage('Maven Build') {
            agent { label 'maven' }
            steps {
                script {
                    sh 'mvn clean package'
                }
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE}:${env.BUILD_NUMBER} ."
                }
            }
        }

        stage('Docker Push') {
            steps {
                script {
                    docker.withRegistry('', DOCKER_REGISTRY_CREDENTIALS_ID) {
                        sh "docker push ${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
                    }
                }
            }
        }

        stage('Kubernetes Deploy') {
            steps {
                script {
                    withCredentials([file(credentialsId: KUBECONFIG_CREDENTIALS_ID, variable: 'KUBECONFIG')]) {
                        sh '''
                            kubectl set image deployment/your-deployment-name \
                            your-container-name=${DOCKER_IMAGE}:${env.BUILD_NUMBER} \
                            --record
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}

