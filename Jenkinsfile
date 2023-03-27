pipeline {
    agent any
    environment {
        PATH=sh(script:"echo $PATH:/usr/local/bin:$HOME/bin", returnStdout:true).trim()
        APP_NAME="jamestask"
        APP_REPO_NAME="james/task"
        AWS_ACCOUNT_ID=sh(script:'export PATH="$PATH:/usr/local/bin" && aws sts get-caller-identity --query Account --output text', returnStdout:true).trim()
        AWS_REGION="us-east-1"
        ECR_REGISTRY="${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
    }
    stages {
        stage('Package Application') {
            steps {
                echo 'Packaging the app into jars with maven'
                sh "./mvnw clean package"
            }
        }
          stage('Build ECR repo') {
            steps {
                PATH="$PATH:/usr/local/bin"
                APP_REPO_NAME="james/task"
                AWS_REGION="us-east-1"

                aws ecr describe-repositories --region ${AWS_REGION} --repository-name ${APP_REPO_NAME} || \
                aws ecr create-repository \
                --repository-name ${APP_REPO_NAME} \
                --image-scanning-configuration scanOnPush=false \
                --image-tag-mutability MUTABLE \
                --region ${AWS_REGION}
            }
        }
            
        
        stage('Build App Docker Images') {
            steps {
                echo 'Building App Dev Images'
                sh 'docker build --force-rm -t jamesfrostyy/exam .'
                sh 'aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_REGISTRY}'
                sh 'docker push jamesfrostyy/exam:latest'
            }
        }
       
        stage('Deploy App on Kubernetes Cluster'){
            steps {
                echo 'Deploying App on Kubernetes Cluster'
                sh 'kubectl apply -f resource.yml'
                sh 'kubectl apply -f ingress.yml'
            }
        }
    }
    
}   