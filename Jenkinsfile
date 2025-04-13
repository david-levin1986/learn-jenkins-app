pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '0ea6e696-d1f5-4606-a4b6-d7baf5580f49'
        NETLIFY_AUTH_TOKEN = credentials('Netlify-Token') 
        REACT_APP_VERSION = "1.0.$BUILD_ID"
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
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            } 
        }

        stage(AWS) {
            agent {
                docker {
                    image 'amazon/aws-cli'
                    reuseNode true
                    args "--entrypoint=''"
                }
            }
            environment {
                MY_BUCKET = 'learn-jenkins-david'
            }
            steps {
                
                withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    // some block
                    sh '''
                    aws --version
                    aws s3 rm s3://$MY_BUCKET --recursive
                    aws s3 sync build s3://$MY_BUCKET
                    '''
                }                
                    
            }
        }

    } 

}