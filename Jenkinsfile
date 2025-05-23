pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'Lulu015/suporte-ti-agendamento-backend'
        DOCKER_TAG = 'latest'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('backend') {
                    script {
                        dockerImage = docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    sh 'kubectl apply -f k8s/mysql-deployment.yaml'
                    sh 'kubectl apply -f k8s/app-deployment.yaml'
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deploy concluído com sucesso.'
        }
        failure {
            echo '❌ Falha na pipeline.'
        }
    }
}
