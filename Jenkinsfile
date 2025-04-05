pipeline {
    agent any

    stages {
        stage('test docker alpine') {
            steps {
                echo 'testing docker alpine'
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
