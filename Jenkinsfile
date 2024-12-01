pipeline {
    agent any

    stages {
        stage('w/o docker') {
            steps {
                sh '''
                    echo "Without docker"
                    ls -la
                    touch container-no.txt
                '''
                
                // sh 'npm --version'
            }
        }
        
        stage('w/ docker') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true  // workspace sync
                }
            }
            steps {
                sh '''
                    echo "With docker"
                   ls -la
                   touch container-yes.txt
                '''
            }
        }
    }
}
