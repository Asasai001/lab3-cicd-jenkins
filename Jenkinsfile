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
        
        stage('Push') {
            steps {
                script {
                    withCredentials([
                        usernamePassword(
                            credentialsId: 'dockerhub',
                            usernameVariable: 'DOCKER_USERNAME',
                            passwordVariable: 'DOCKER_PASSWORD'
                        )
                        ]) {
                            sh '''
                                echo "$DOCKER_PASSWORD" | docker login \
                                    -u "$DOCKER_USERNAME" \
                                    --password-stdin
                            '''
                
                        if (env.BRANCH_NAME == 'main') {
                            sh '''
                                docker tag nodemain:v1.0 asasai001/lab3-cicd:nodemain-v1.0
                                docker push asasai001/lab3-cicd:nodemain-v1.0
                            '''
                        } else if (env.BRANCH_NAME == 'dev') {
                            sh '''
                                docker tag nodedev:v1.0 asasai001/lab3-cicd:nodedev-v1.0
                                docker push asasai001/lab3-cicd:nodedev-v1.0
                            '''
                        }
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh '''
                            docker rm -f nodemain || true
                            docker run -d -p 3000:3000 --name nodemain nodemain:v1.0
                        '''
                    } else if (env.BRANCH_NAME == 'dev') {
                        sh '''
                            docker rm -f nodedev || true
                            docker run -d -p 3001:3000 --name nodedev nodedev:v1.0
                        '''
                    }
                }
            }
        }
    }
}
