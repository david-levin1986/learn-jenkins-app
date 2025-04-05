pipeline {
    agent any

    stages {
        stage('Hello') {
            steps {
                echo 'Hello World'
            }
        }
        stage('Test Docker alpine') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
    }
}
