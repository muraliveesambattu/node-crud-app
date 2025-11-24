pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm ci || npm install'
            }
        }

        stage('Start App') {
            steps {
                // Start Node app in background
                sh 'npm run dev &'

                // Wait until the app is up
                sh 'npx wait-on http://localhost:3000'
            }
        }

        stage('Run Cypress E2E Tests') {
            steps {
                sh 'npm run test:e2e'
            }
        }
    }

    post {
        success {
            echo '✅ All stages passed. Cypress tests are green.'
        }
        failure {
            echo '❌ Pipeline failed. Check logs above.'
        }
    }
}
