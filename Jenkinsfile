pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '0ea6e696-d1f5-4606-a4b6-d7baf5580f49'
        NETLIFY_AUTH_TOKEN = credentials('Netlify-Token') 
        REACT_APP_VERSION = "1.0.$BUILD_ID"
    }

    stages {

        stage('AWS') {
         agent {
            docker {
                image 'amazon/aws-cli'
                args "--entrypoint=''"
            }
         }
         steps {
            sh '''
                aws --version
            '''
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

        stage ('Test') {
            parallel {
                stage ('Unit Test') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                        echo "Starting Test stage"
                        test -f build/index.html
                        npm test
                        '''
                    }
                    post {
                        always {
                            junit 'jest-results/junit.xml'
                        }
                    }                    
                }

                stage ('My Custom Docker') {
                    steps {
                        sh '''
                        echo "Buildin Docker my-ms-playwright"
                        '''
                    }
                }

                stage ('E2E Local') {
                    agent {
                        docker {
                            image 'my-ms-playwright'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                        echo "Starting Test stage"
                        serve -s build &
                        sleep 10
                        npx playwright test --reporter=html
                        '''
                    }

                    post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright Local', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }

                }               
            }
        }

        stage('Deploy Dev') {
            
            agent {
                docker {
                    image 'my-ms-playwright'
                    reuseNode true
                }
            }
             steps {
                sh '''
                netlify --version  
                echo "Deploing to SiteDev Site ID: $NETLIFY_SITE_ID"
                netlify status
                netlify deploy --dir=build --json > deploy-output.json
                jq -r '.deploy_url' deploy-output.json
                '''
                script {
                env.Dev_URL = sh(script: "jq -r '.deploy_url' deploy-output.json", returnStdout: true)
                }
            
            } 
               
        }
                stage ('Dev E2E') {
            agent {
                docker {
                    image 'my-ms-playwright'
                    reuseNode true
                }
            }
            environment {
                CI_ENVIRONMENT_URL = "${env.Dev_URL}"
            }

            steps {
                sh '''
                npx playwright test --reporter=html
                '''
            }

            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Dev E2E', reportTitles: '', useWrapperFileDirectly: true])
                }
            }

        } 
                stage('Deploy Prod') {
            
            agent {
                docker {
                    image 'my-ms-playwright'
                    reuseNode true
                }
            }
             steps {
                sh '''
                netlify --version  
                echo "Deploing to Site ID: $NETLIFY_SITE_ID"
                netlify status
                netlify deploy --dir=build --prod
                '''
            } 
        }

                

                stage ('Prod E2E') {
            agent {
                docker {
                    image 'my-ms-playwright'
                    reuseNode true
                }
            }
            environment {
                CI_ENVIRONMENT_URL = 'https://astounding-beignet-b6814d.netlify.app'
            }

            steps {
                sh '''
                npx playwright test --reporter=html
                '''
            }

            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Prod E2E', reportTitles: '', useWrapperFileDirectly: true])
                    
                }
            }

        } 

    }

}
