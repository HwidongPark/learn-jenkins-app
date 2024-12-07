pipeline {
    agent any

    stages {
        // This is a comment
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls  -la

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
                    echo "Test started ..."
                    test -f build/index.html
                    
                    npm test

                    echo "Test successfully ended ..."
                '''
            }
        }

        stage('E2E') {
            agent {
                docker {
                    // 
                    image 'mcr.microsoft.com/playwright:v1.49.0-noble'
                    reuseNode true
                    // 방법 1) root 유저로 실행
                    // serve -s build가 root 유저 아니면 실행 안되기 때문
                    // args '-u root:root'
                }
            }

            steps {
                sh '''
                    npm install -g serve
                    # serve -s build
                    node_modules/.bin/serve -s build
                    npx playwrite test
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
