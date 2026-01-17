pipeline {
    agent any

    environment {
        IMAGE_NAME = "devops-app:latest"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/eswar199918/devops-basic-project.git'
            }
        }

        stage('SonarQube Code Scan') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh 'sonar-scanner'
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Trivy Image Scan') {
            steps {
                sh 'trivy image --severity HIGH,CRITICAL --exit-code 0 $IMAGE_NAME'
            }
        }

        stage('Deploy to K3s') {
            steps {
                sh 'kubectl apply -f k8s/'
            }
        }
    }
}
