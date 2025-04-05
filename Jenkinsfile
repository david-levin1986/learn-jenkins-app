pipeline {
    agent any

    stages {
        stage('Docker Alpine') {
            steps {
                echo 'building Docker Alpine'
            }
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
        }
    }
}
