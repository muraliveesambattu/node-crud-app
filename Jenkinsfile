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
     stage('Install Cypress System Dependencies')  // Fixed version
{
    steps {
        sh '''
            # Check if we are on ARM64
            if [ "$(uname -m)" = "aarch64" ] || [ "$(uname -m)" = "arm64" ]; then
                echo "ARM64 detected – installing missing Cypress dependencies as root..."

                # This is the key: run apt-get with sudo (or as root)
                # Jenkins agents usually have sudo without password for the jenkins user
                sudo apt-get update -qq
                sudo apt-get install -y --no-install-recommends \
                    libcups2 \
                    libgtk-3-0 \
                    libgbm-dev \
                    libnss3 \
                    libasound2 \
                    libxtst6 \
                    xvfb \
                    fonts-liberation

                # Clean up
                sudo rm -rf /var/lib/apt/lists/*
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
