pipeline {
    agent any

    environment {
        REACT_APP_VERSION = "1.0.$BUILD_ID"
        APP_NAME = 'myjenkinsapp'
        AWS_DOCKER_REGESTRY = '012249777837.dkr.ecr.us-east-1.amazonaws.com'
        AWS_DEFAULT_REGION = 'us-east-1'
        AWC_ECS_CLS = 'Jenkins-Cls-Prd'
        AWS_ECS_SERVICE = 'JenkinsApp-Service-Prod'
        AWS_ECS_TASK = 'JenkinsApp-TaskDefinition-Prod'
    }

    stages {


    
        stage('Build') {
            
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
             steps {
                sh '''                
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            } 
        }

        stage('Build Docker Image') {
            agent {
                docker {
                    image 'my-aws-cli'
                    reuseNode true
                    args "-u root -v /var/run/docker.sock:/var/run/docker.sock --entrypoint=''"
                }
            }
            steps {
                 withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    // some block
                     sh '''
                    docker build -t $AWS_DOCKER_REGESTRY/$APP_NAME:$REACT_APP_VERSION .
                    aws ecr get-login-password | docker login --username AWS --password-stdin $AWS_DOCKER_REGESTRY
                    
                    docker push $AWS_DOCKER_REGESTRY/$APP_NAME:$REACT_APP_VERSION
                '''                  
                }               
                
            }
        }
        
        stage('Deploy to AWS') {
            agent {
                docker {
                    image 'my-aws-cli'
                    reuseNode true
                    args "--entrypoint=''"
                }
            }
            
            steps {
                
                withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    // some block
                    sh '''
                    aws --version
                    sed -i "s/#APP_VERSION#/$REACT_APP_VERSION"/g AWS/task-definition-prod.json
                    LATES_REVISION=$(aws ecs register-task-definition --cli-input-json file://AWS/task-definition-prod.json | jq '.taskDefinition.revision')
                    aws ecs update-service --cluster $AWC_ECS_CLS --service $AWS_ECS_SERVICE --task-definition $AWS_ECS_TASK:$LATES_REVISION
                    aws ecs wait services-stable --cluster $AWC_ECS_CLS --service $AWS_ECS_SERVICE
                    '''
                }                
                    
            }
        }


    } 

}