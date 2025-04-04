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
    }
    
    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
    
}
