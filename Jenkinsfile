pipeline {
    agent any
    
    tools {
        nodejs 'NodeJS-7.8.0'
    }
    
    stages {
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        
        stage('Test Application') {
            steps {
                sh 'npm test'
            }
        }
        
        stage('Build Docker Image') {
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
        
        stage('Deploy Container') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        // Minimal downtime container swap on port 3000
                        sh '''
                            docker stop nodemain_app || true
                            docker rm nodemain_app || true
                            docker run -d --name nodemain_app --expose 3000 -p 3000:3000 nodemain:v1.0
                        '''
                    } else if (env.BRANCH_NAME == 'dev') {
                        // Minimal downtime container swap on port 3001
                        sh '''
                            docker stop nodedev_app || true
                            docker rm nodedev_app || true
                            docker run -d --name nodedev_app --expose 3001 -p 3001:3000 nodedev:v1.0
                        '''
                    }
                }
            }
        }
    }
}
