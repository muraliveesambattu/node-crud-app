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
                sh 'npm ci'
            }
        }

        // This new stage fixes your Cypress error on ARM64
        stage('Install Cypress System Dependencies') {
            steps {
                sh '''
                    # Detect if we are running on ARM64 (your Jenkins node is arm64)
                    if [ "$(uname -m)" = "aarch64" ] || [ "$(uname -m)" = "arm64" ]; then
                        echo "ARM64 detected – installing missing Cypress dependencies..."
                        apt-get update -qq
                        apt-get install -y --no-install-recommends \
                            libcups2 \
                            libgtk-3-0 \
                            libgbm-dev \
                            libnss3 \
                            libasound2 \
                            libxtst6 \
                            xvfb \
                            fonts-liberation
                        rm -rf /var/lib/apt/lists/*
                    else
                        echo "Not ARM64 – skipping system dependencies"
                    fi
                '''
            }
        }

        stage('Start App') {
            steps {
                sh 'npm run dev &'          // start the Node.js app in background
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
        always {
            echo 'Pipeline finished.'
        }
        failure {
            echo 'Pipeline failed. Check logs (Cypress or Node error).'
        }
    }
}
