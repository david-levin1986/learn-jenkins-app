pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '0ea6e696-d1f5-4606-a4b6-d7baf5580f49'
        NETLIFY_AUTH_TOKEN = credentials('Netlify-Token') 
        REACT_APP_VERSION = "1.0.$BUILD_ID"
    }

    stages {
        stage('Docker') {
            steps {
                sh 'docker build -t my-ms-playwright .'
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

                stage ('Massege Microdoft Docker') {
                    steps {
                        sh '''
                        echo "Buildin Docker Microsft"
                        '''
                    }
                }

                stage ('E2E Local') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                        echo "Starting Test stage"
                        npm install serve
                        node_modules/.bin/serve -s build &
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
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
             steps {
                sh '''
                echo "Start Installitin of netlify-cli"
                npm install netlify-cli node-jq
                node_modules/.bin/netlify --version  
                echo "Deploing to SiteDev Site ID: $NETLIFY_SITE_ID"
                node_modules/.bin/netlify status
                node_modules/.bin/netlify deploy --dir=build --json > deploy-output.json
                node_modules/.bin/node-jq -r '.deploy_url' deploy-output.json
                '''
                script {
                env.Dev_URL = sh(script: "node_modules/.bin/node-jq -r '.deploy_url' deploy-output.json", returnStdout: true)
                }
            
            } 
               
        }
                stage ('Dev E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
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
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
             steps {
                sh '''
                echo "Start Installitin of netlify-cli"
                npm install netlify-cli node-jq
                node_modules/.bin/netlify --version  
                echo "Deploing to Site ID: $NETLIFY_SITE_ID"
                node_modules/.bin/netlify status
                node_modules/.bin/netlify deploy --dir=build --prod
                '''
            } 
        }

                

                stage ('Prod E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
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
