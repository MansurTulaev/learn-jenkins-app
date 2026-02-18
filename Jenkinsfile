pipeline {
    agent any
    environment {
        BUILD_FILE_NAME = 'build/index.html'
    }
    stages {
        /*
        stage('Build') {
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
        */
        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                //cleanWs()
                sh '''
                    #echo 'Test stage'
                    echo $BUILD_FILE_NAME
                    test -f $BUILD_FILE_NAME
                    npm test
                '''
            }
        }
        stage('End to end test') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                    // dont use! args '-u  root:root'
                }
            }
            steps {
                //cleanWs()
                sh '''
                    npm install serve
                    node_modules/.bin/serve -s build &
                    sleep 10
                    npx playwright test --reporter=html
                '''
            }
        }
    }

    post {
        always {
            junit 'jest-results/junit.xml'
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright: HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        }
    }
}
