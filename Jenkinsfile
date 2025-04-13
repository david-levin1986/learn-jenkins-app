pipeline {
    agent any

    environment {
        REACT_APP_VERSION = "1.0.$BUILD_ID"
        AWS_DEFAULT_REGION = 'us-east-1'
        AWC_ECS_CLS = 'Jenkins-Cls-Prd'
        AWS_ECS_SERVICE = 'JenkinsApp-Service-Prod'
        AWS_ECS_TASK = 'JenkinsApp-TaskDefinition-Prod'
    }

    stages {

        stage('Deploy to AWS') {
            agent {
                docker {
                    image 'amazon/aws-cli'
                    reuseNode true
                    args "-u root --entrypoint=''"
                }
            }
            
            steps {
                
                withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    // some block
                    sh '''
                    aws --version
                    yum install jq -y
                    LATES_REVISION=$(aws ecs register-task-definition --cli-input-json file://AWS/task-definition-prod.json | jq '.taskDefinition.revision')
                    aws ecs update-service --cluster $AWC_ECS_CLS --service $AWS_ECS_SERVICE --task-definition $AWS_ECS_TASK:$LATES_REVISION
                    aws ecs wait services-stable --cluster $AWC_ECS_CLS --service $AWS_ECS_SERVICE
                    '''
                }                
                    
            }
        }
    
        stage('Build') {
            
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
             steps {
                sh '''                
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            } 
        }



    } 

}