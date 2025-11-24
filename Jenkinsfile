pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // If using "Pipeline script from SCM", this is optional
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                // npm ci is nicer in CI, falls back to npm install if no lock
                sh 'npm ci || npm install'
            }
        }

        stage('Start App') {
            steps {
                // Start the app in background
                sh 'npm run dev &'

                // Wait until http://localhost:3000 is up
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
            echo '✅ All stages passed. Cypress green!'
        }
        failure {
            echo '❌ Pipeline failed. Check logs (Cypress or Node error).'
        }
    }
}
