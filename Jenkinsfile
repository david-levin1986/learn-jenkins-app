pipeline {
    agent any

    environment {
        REACT_APP_VERSION = "1.0.$BUILD_ID"
        AWS_DEFAULT_REGION = 'us-east-1'
    }

    stages {

        stage('Deploy to AWS') {
            agent {
                docker {
                    image 'amazon/aws-cli'
                    reuseNode true
                    args "-u --entrypoint=''"
                }
            }
            
            steps {
                
                withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    // some block
                    sh '''
                    aws --version
                    dnf install jq -y
                    LATES_REVISION=$(aws ecs register-task-definition --cli-input-json file://AWS/task-definition-prod.json | jq '.taskDefinition.revision')
                    echo $LATES_REVISION
                    aws ecs update-service --cluster Jenkins-Cls-Prd --service JenkinsApp-Service-Prod --task-definition JenkinsApp-TaskDefinition-Prod:$LATES_REVISION
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