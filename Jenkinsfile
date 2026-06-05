pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = "ap-south-1"   // change to your region
        ECR_REPO = "286008326537.dkr.ecr.ap-south-1.amazonaws.com/my-nginx-app:latest"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Build') {
            steps {
                script {
                    sh 'docker build -t my-nginx-app .'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // simple container test
                    sh 'docker run --rm my-nginx-app nginx -v'
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    sh 'aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin $ECR_REPO'
                    sh 'docker tag my-nginx-app:latest $ECR_REPO:$IMAGE_TAG'
                    sh 'docker push $ECR_REPO:$IMAGE_TAG'
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                script {
                    sh 'kubectl apply -f deployment.yaml'
                    sh 'kubectl apply -f service.yaml'
                }
            }
        }
    }
}
