pipeline {
    environment {
        DOCKER_IMAGE = '[YOUR_DOCKERHUB_USERNAME]/[YOUR_IMAGE_NAME]'
        REGISTRY_CREDENTIALS_ID = 'docker-hub-credentials'
        KUBE_CONFIG = credentials('[YOUR_KUBE_CONFIG]')
    }
    agent any
    stages {
        stage('Checkout code') {
            steps {
                checkout scm
            }
        }
        stage('Build image') {
            steps {
                script {
                    docker.build("$DOCKER_IMAGE:$BUILD_NUMBER")
                }
            }
        }
        stage('Push image') {
            steps {
                script {
                    docker.withRegistry('', REGISTRY_CREDENTIALS_ID) {
                        docker.image("$DOCKER_IMAGE:$BUILD_NUMBER").push()
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Set up Kubectl
                    withKubeConfig([credentialsId: 'k8s-kubeconfig']) {
                        // Run the kubectl commands to deploy to Kubernetes
                        sh "kubectl apply -f [YOUR_DEPLOYMENT_FILE] --namespace your-namespace"
                        sh "kubectl set image deployment/your-deployment your-container=$DOCKER_IMAGE:$BUILD_NUMBER --namespace your-namespace"
                    }
                }
            }
        }
    }
    post {
        success {
            echo 'The pipeline has completed successfully!'
        }
    }
}
