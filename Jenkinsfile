pipeline {
    agent any

    stages {
        stage('Massege') {
            steps {
                sh '''
                echo "Building Docker Alpine"
                '''
                
            }
        }
        stage('Docker Alpine') {
            
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
             steps {
                sh '''
                    echo "starting npm"
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
        }

        stage ('Massege Microdoft Docker') {
            steps {
                sh '''
                echo "Buildin Docker Microsft"
                date +%H:%M:%S
                echo "this step will take abut 12 minets"
                '''
            }
        }

        stage ('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }
            steps {
                sh '''
                date +%H:%M:%S
                echo "Starting Test stage"
                test -f build/index.html
                npm test
                '''
            }
        }

    }
}
