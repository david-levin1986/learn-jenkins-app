pipeline {
    agent any

    stages {
        stage('Build') {
            /* 
            stage Build
            crate docker alpine
            set all file to be in same location
            */
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
        stage('Test') {
             agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                echo "Starting Test Stage"
                test -f build/index.html
                npm test
                '''
            }

        }

                stage('E2E') {
             agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }
            steps {
                sh '''
                echo "Starting E2E"
                npm install serve
                serve -s build
                npx playwright test
                '''
            }

        }

    }
    
    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
    
}
