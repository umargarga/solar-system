pipeline {
    agent any

    tools {
        nodejs 'nodejs-22-18-0'
    }

    environment {
        MONGO_URI = "mongodb+srv://supercluster.d83jj.mongodb.net/superData"
        MONGO_DB_CREDS = credentials('mongo-db-credentials')
        MONGO_USERNAME = credentials('mongo-db-usernmae')
        MONGO_PASSWORD = credentials('mongo-db-password')

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
                catchError(buildResult: 'SUCCESS', message: 'Opps error', stageResult: 'UNSTABLE') {
                    sh 'echo $MONGO_DB_CREDS'
                    sh 'echo Username - $MONGO_DB_CREDS_USR'
                    sh 'echo Password - $MONGO_DB_CREDS_PSW'
                    sh 'npm test' 
                }
            }
        }

        stage('Code Coverage') {
            steps {
                catchError(buildResult: 'SUCCESS', message: 'Opps error', stageResult: 'UNSTABLE') {
                    sh 'npm run coverage'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t umargarga/solar-system:$GIT_COMMIT .'
            }
        }

        // stage('Trivy Vulnerability Scanner') {
        //     steps {
        //         sh '''
        //             trivy image umargarge/solar-system:$GIT_COMMIT \
        //                 -- severity LOW,MEDIUM,HIGH \
        //                 --exit-code 0 \
        //                 --quiet \
        //                 --format json -o trivy-image-MEDIUM-results.json

        //             trivy image umargarga/solar-system:$GIT_COMMIT \
        //                 -- severity CRITICAL \
        //                 --exit-code 1 \
        //                 --quiet \
        //                 --format json -o trivy-image-CRITICAL-results.json
        //         '''
        //     }
           
        // }

        stage('Push Docker Image') {
            steps {
                withDockerRegistry(credentialsId: 'docker-hub-credentials', url: "") {
                    sh 'docker push -t umargarga/solar-system:$GIT_COMMIT .'
                }
                
            }
        }

    }
}