pipeline {
    agent any

    environment {
        AWS_REGION = "ap-south-1"
        ACCOUNT_ID = "439490335073"
        ECR = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/hello-app"
    }

    stages {

        stage('Build') {
            steps {
                sh 'docker build -t hello-app .'
            }
        }

        stage('Login ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION | docker login \
                --username AWS --password-stdin $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                '''
            }
        }

        stage('Push') {
            steps {
                sh '''
                docker tag hello-app:latest $ECR:latest
                docker push $ECR:latest
                '''
            }
        }

        stage('Configure Kube') {
            steps {
                sh '''
                aws eks update-kubeconfig --region $AWS_REGION --name hello-cluster
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                '''
            }
        }
    }
}