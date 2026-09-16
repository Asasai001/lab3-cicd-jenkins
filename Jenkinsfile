pipeline {
    agent any
    
    tools {
        nodejs 'node'
    }
    
    
    stages {
    
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                sh 'scripts/build.sh'
            }
        }
        
        stage('Test') {
            steps {
                sh 'scripts/test.sh'
            }
        }
        
        stage('Docker Build') {
           steps {
               script {
                   if (env.BRANCH_NAME == 'main') {
                       sh 'docker build -t nodemain:v1.0 .'
                   } else if (env.BRANCH_NAME == 'dev') {
                       sh 'docker build -t nodedev:v1.0 .'
                   }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh 'docker run -d -p 3000:3000 --name nodemain nodemain:v1.0'
                    } else if (env.BRANCH_NAME == 'dev') {
                        sh 'docker run -d -p 3001:3000 --name nodedev nodedev:v1.0'
                    }
                }
            }
        }
    }
}
