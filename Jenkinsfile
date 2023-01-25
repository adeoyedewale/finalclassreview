pipeline {
    agent any
  
    environment {
	    DOCKERHUB_CREDENTIALS=credentials('dockerhub')
    }

    stages {
      stage('Checkout Code') {
            steps {
                // Checkout the code from the repository
                git url: 'https://github.com/adeoyedewale/finalclassreview.git'
            }
        }
	    
        stage('Build Backend') {
            steps {
                sh 'cd backend && npm ci && npm install'
                sh 'cd ..'
            }
        }
            
        stage('Build Frontend') {
            steps {
                sh 'cd frontend && npm ci && npm install && npm run build'
                sh 'cd ..'
            }
        }
        
        stage('Create Docker Image') {
            steps {
                sh 'docker build -t eruobodo/myximage:$BUILD_NUMBER .'
            }
        }
            
            //stage('Build') {
            //    steps{
            //        sh 'docker-compose build'
            //    }
        // }
        stage('Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
                    //sh 'docker-compose push myapp'
                    sh 'docker push eruobodo/myximage:$BUILD_NUMBER'
                }
            }
        }
        stage ('Publish to ECR') {
            steps {
            //sh 'aws ecr-public get-login-password --region eu-west-2 | docker login --username AWS --password-stdin public.ecr.aws/t7e2c6o4'
            //withAWS(credentials: 'sam-jenkins-demo-credentials', region: 'eu-west-2') {
                withEnv(["AWS_ACCESS_KEY_ID=${env.AWS_ACCESS_KEY_ID}", "AWS_SECRET_ACCESS_KEY=${env.AWS_SECRET_ACCESS_KEY}", "AWS_DEFAULT_REGION=${env.AWS_DEFAULT_REGION}"]) {
                    sh 'docker login -u AWS -p $(aws ecr get-login-password --region us-east-1) public.ecr.aws/c6p1p1z3' //985729960198.dkr.ecr.eu-west-2.amazonaws.com'
                    sh 'docker tag eruobodo/myximage:$BUILD_NUMBER public.ecr.aws/c6p1p1z3/devops-code-challenge:$BUILD_NUMBER'
                    sh 'docker push public.ecr.aws/c6p1p1z3/devops-code-challenge:$BUILD_NUMBER'
                }
            }
        }
    }
    post {
            always {
                cleanWs()
                sh 'docker logout'
            }
        }
} 
