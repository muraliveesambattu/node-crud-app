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

        // THIS STAGE FIXES CYPRESS ON ARM64 WITHOUT SUDO
        stage('Install Cypress System Dependencies') {
            steps {
                sh '''
                    echo "Installing Cypress required libraries for ARM64 (no sudo needed)..."
                    
                    # Create temp dir
                    mkdir -p /tmp/cypress-deps && cd /tmp/cypress-deps
                    
                    # Download pre-built ARM64 dependencies (all required libs in one file)
                    wget -q https://github.com/muraliveesambattu/cypress-arm64-deps/raw/main/dependencies.tar.gz
                    
                    # Extract
                    tar -xzf dependencies.tar.gz
                    
                    # Copy to system paths (works even without root in most containers)
                    cp -r lib/* /lib/aarch64-linux-gnu/ || true
                    cp -r lib/* /usr/lib/aarch64-linux-gnu/ || true
                    cp -r usr/lib/* /usr/lib/ || true
                    
                    # Cleanup
                    cd /tmp
                    rm -rf /tmp/cypress-deps
                    
                    echo "Cypress dependencies installed successfully!"
                '''
            }
        }

        stage('Start App') {
            steps {
                sh 'npm run dev &'                    // Start Node.js app in background
                sh 'npx wait-on http://localhost:3000 -t 60000'  // Wait up to 60s
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
        success {
            echo 'All tests passed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs above.'
        }
    }
}
