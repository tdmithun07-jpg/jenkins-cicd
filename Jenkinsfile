pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        ECR_REPO = "888154844094.dkr.ecr.us-east-1.amazonaws.com/jenkins-2801"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/tdmithun07-jpg/jenkins-cicd.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${ECR_REPO}:${IMAGE_TAG}")
                }
            }
        }

        stage('Login to ECR') {
            steps {
                script {
                    sh '''
                    aws ecr get-login-password --region $AWS_REGION | \
                    docker login --username AWS --password-stdin $ECR_REPO
                    '''
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    sh "docker push ${ECR_REPO}:${IMAGE_TAG}"
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                script {
                    sh '''
                    aws eks update-kubeconfig --name cluster-2901 --region $AWS_REGION
                    kubectl apply -f Deployment.yaml
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "🎉 Deployment successful! Check your LoadBalancer URL."
        }
        failure {
            echo "❌ Deployment failed."
        }
    }
}
