pipeline {
    agent any

    environment {
        IMAGE_NAME = "eswar199918/flask-devops"
        TAG = "latest"
        K8S_IP = "54.165.171.216"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/eswar199918/app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${TAG} ."
            }
        }

        stage('Login DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                }
            }
        }

        stage('Push Image') {
            steps {
                sh "docker push ${IMAGE_NAME}:${TAG}"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                ssh -o StrictHostKeyChecking=no ubuntu@${K8S_IP} 'kubectl apply -f -' < k8s/namespace.yaml
                ssh -o StrictHostKeyChecking=no ubuntu@${K8S_IP} 'kubectl apply -f -' < k8s/deployment.yaml
                ssh -o StrictHostKeyChecking=no ubuntu@${K8S_IP} 'kubectl apply -f -' < k8s/service.yaml
                ssh -o StrictHostKeyChecking=no ubuntu@${K8S_IP} 'kubectl apply -f -' < k8s/ingress.yaml
                """
            }
        }
    }
}
