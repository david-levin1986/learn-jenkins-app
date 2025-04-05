pipeline {
    agent any

    stages {
        stage('Massege') {
            steps {
                '''
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
                    npm cli
                    npm run build
                    ls -la
                '''
            } 
        }
    }
}
