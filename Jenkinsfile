pipeline {
    agent any

    stages {
        stage('test docker jammy') {
            steps {
                echo 'testing docker jammy'
            }
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }
           
        }
        
    }
}
