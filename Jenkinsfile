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
            sh 'scripts/build.sh'
        }
        
        stage('Test') {
            sh 'scripts/test.sh'
        }
        
        stage('Docker Build') {
           steps {
               sh 'docker build -t my-app:0.1.0 .'
           }
        }
        
        stage('Deploy') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh 'docker run -d -p 3000:3000 my-app:0.1.0'
                    } else if (env.BRANCH_NAME == 'dev') {
                        sh 'docker run -d -p 3001:3001 my-app:0.1.0'
                    }
                }
            }
        }
    }
