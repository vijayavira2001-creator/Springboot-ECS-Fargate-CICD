pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REPOSITORY = 'springboot-aws-deploy'
        IMAGE_TAG = 'v1'
        AWS_ACCOUNT_ID = '705505267955'
        ECR_REGISTRY = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
        ECR_IMAGE = "${ECR_REGISTRY}/${ECR_REPOSITORY}:${IMAGE_TAG}"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/vijayavira2001-creator/Springboot-ECS-Fargate-CICD.git'
            }
        }

        stage('Build') {
            steps {
                sh '''
                    export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
                    export PATH=$JAVA_HOME/bin:$PATH
                    mvn clean package
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t springboot-aws-deploy:v1 .'
            }
        }

        stage('ECR Login') {
            steps {
                sh '''
                    aws ecr get-login-password --region $AWS_REGION |
                    docker login --username AWS --password-stdin $ECR_REGISTRY
                '''
            }
        }

        stage('Push to ECR') {
            steps {
                sh '''
                    docker tag springboot-aws-deploy:v1 $ECR_IMAGE
                    docker push $ECR_IMAGE
                '''
            }
        }

        stage('Deploy to ECS') {
            steps {
                sh '''
                    aws ecs update-service \
                        --cluster Springboot-ECS-Cluster \
                        --service springboot-ecs-service \
                        --force-new-deployment \
                        --region $AWS_REGION
                '''
            }
        }
    }
}
