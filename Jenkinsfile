pipeline {
    agent any

    tools {
        nodejs 'nodejs-22-18-0'
    }

    environment {
        MONGO_URI = "mongodb+srv://supercluster.d83jj.mongodb.net/superData"
    }

    stages {
        stage('Installing Dependencies') {
            options { timestamps() }
            steps {
                sh 'npm install --no-audit'
            }
        }

        stage('NPM Dependencies Audit') {
            steps {
                sh '''
                    npm audit --audit-level=critical
                    echo $?
                '''
                }
        }

        stage('Unit Testing') {
            // options { retry(2) }
            steps {
                withCredentials([usernamePassword(credentialsId: 'mongo-db-credentials', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {
                    catchError(buildResult: 'SUCCESS', message: 'Opps error', stageResult: 'UNSTABLE') {
                        sh 'npm test' 
                    }
                    
                }
            }
        }

        stage('Code Coverage') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'mongo-db-credentials', passwordVariable: 'MONGO_PASSWORD', usernameVariable: 'MONGO_USERNAME')]) {
                    catchError(buildResult: 'SUCCESS', message: 'Opps error', stageResult: 'UNSTABLE') {
                        sh 'npm run coverage'
                    }

                }
            }
        }

    }
}