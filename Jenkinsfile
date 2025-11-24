pipeline {
    agent any

    stages {
        stage('Checkout') { /* your existing checkout stage */ }

        stage('Install Dependencies') {
            steps {
                sh 'npm ci'
            }
        }

        // ADD THIS NEW STAGE HERE
        stage('Install Cypress System Dependencies') {
            steps {
                sh '''
                    # Only run if we are on Linux ARM64 and the package manager is apt
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
                            xvfb
                        rm -rf /var/lib/apt/lists/*
                    else
                        echo "Not ARM64 – skipping system dependency installation"
                    fi
                '''
            }
        }
        // END OF NEW STAGE

        stage('Start App') {
            steps {
                sh 'npm run dev &'
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
        failure {
            echo 'Pipeline failed. Check logs (Cypress or Node error).'
        }
    }
}
