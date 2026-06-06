pipeline {
agent any

```
environment {
    AWS_DEFAULT_REGION = "ap-south-1"
    ECR_REGISTRY = "286008326537.dkr.ecr.ap-south-1.amazonaws.com"
    ECR_REPO = "286008326537.dkr.ecr.ap-south-1.amazonaws.com/dockerimage"
    IMAGE_TAG = "latest"

    // Replace with your EKS cluster name
    EKS_CLUSTER_NAME = "charming-bluegrass-mushroom"
}

stages {

    stage('Debug') {
        steps {
            bat 'echo AWS_DEFAULT_REGION=%AWS_DEFAULT_REGION%'
            bat 'echo ECR_REGISTRY=%ECR_REGISTRY%'
            bat 'echo ECR_REPO=%ECR_REPO%'
        }
    }

    stage('Build') {
        steps {
            bat 'docker build -t my-nginx-app .'
        }
    }

    stage('Test') {
        steps {
            bat 'docker run --rm my-nginx-app nginx -v'
        }
    }

    stage('Push to ECR') {
        steps {
            withCredentials([
                usernamePassword(
                    credentialsId: 'aws-creds',
                    usernameVariable: 'AWS_ACCESS_KEY_ID',
                    passwordVariable: 'AWS_SECRET_ACCESS_KEY'
                )
            ]) {

                bat 'aws sts get-caller-identity'

                bat '''
                aws ecr get-login-password --region %AWS_DEFAULT_REGION% | docker login --username AWS --password-stdin %ECR_REGISTRY%
                '''

                bat '''
                docker tag my-nginx-app:latest %ECR_REPO%:%IMAGE_TAG%
                '''

                bat '''
                docker push %ECR_REPO%:%IMAGE_TAG%
                '''
            }
        }
    }

    stage('Configure EKS') {
        steps {
            bat '''
            aws eks update-kubeconfig --region %AWS_DEFAULT_REGION% --name %EKS_CLUSTER_NAME%
            '''

            bat 'kubectl config current-context'
            bat 'kubectl get nodes'
        }
    }

    stage('Deploy to EKS') {
        steps {
            bat 'kubectl apply -f deployment.yaml --validate=false'
            bat 'kubectl apply -f service.yaml --validate=false'
            bat 'kubectl rollout status deployment/nginx-web-deployment'
        }
    }
}

post {
    success {
        echo 'Pipeline executed successfully!'
    }

    failure {
        echo 'Pipeline failed. Check console logs.'
    }
}
```

}
