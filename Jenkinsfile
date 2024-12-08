pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '1d1e0a88-d8eb-40ef-ac0c-899b9f7ed711'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

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
                    echo "small change"
                    ls  -la

                    node --version
                    npm --version

                    npm ci
                    npm run build

                    ls -la
                '''
            }
        }
        

        stage('Run Tests') {
            parallel {
                stage('Unit tests') {
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

                    post {
                        always {
                            junit 'test-results/junit.xml'
                        }
                    }
                }   

                /*
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
                            npm install serve
                            # serve -s build
                            node_modules/.bin/serve -s build
                            npx playwrite test
                        '''
                    }

                    post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwrite-report', reportFiles: 'index.html', reportName: 'Playwrite HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
                }  
                */                
            }
        }

        stage ("Deploy") {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    echo "Deploying to production. SITE ID: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }        
        }
    }
}
