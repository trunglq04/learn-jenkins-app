pipeline {
    agent any

    
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

      stage('Tests') {
        parallel {
          stage('Unit tests') {
            agent {
              docker {
                image 'node:18-alpine'
                reuseNode true
              }
            }
          
            steps {
                echo "Test stage"
                sh '''
                  #test -f build/index.html
                  npm test
                '''
            }
          }

          stage('E2E') {
            agent {
              docker {
                image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                reuseNode true
                // Should not use as root user, instead use local app from installed package.
                // args '-u root:root' 
              }
            }

            steps {
              sh '''
                npm i serve
                node_modules/.bin/serve -s build & 
                sleep 10
                npx playwright test --reporter=html
              '''
            }
          }
        }
      }

      

    }

    post {
      always {
        // junit 'jest-results/junit.xml'
        publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
      }
    }
}
