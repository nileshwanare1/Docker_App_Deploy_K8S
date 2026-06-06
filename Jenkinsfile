pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = "ap-south-1"   // change to your region
        ECR_REPO = "286008326537.dkr.ecr.ap-south-1.amazonaws.com"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Build') {
            steps {
                script {
                    bat 'docker build -t my-nginx-app .'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // simple container test
                    bat 'docker run --rm my-nginx-app nginx -v'
                }
            }
        }

        stage('Pubat to ECR') {
            steps {
                script {
                    bat 'aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin $ECR_REPO'
                    bat 'docker tag my-nginx-app:latest $ECR_REPO:$IMAGE_TAG'
                    bat 'docker pubat $ECR_REPO:$IMAGE_TAG'
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                script {
                    bat 'kubectl apply -f deployment.yaml'
                    bat 'kubectl apply -f service.yaml'
                }
            }
        }
    }
}
