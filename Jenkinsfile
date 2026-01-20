pipeline {
    agent any

    tools {
        nodejs 'nodejs-22-18-0'
    }


    stages {
        stage('VM Node Version') {
            steps {
                sh '''
                    node -v
                    npm -v
                '''
            }
        }
    }
}