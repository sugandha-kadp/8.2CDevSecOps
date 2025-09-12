pipeline {
    agent any

    environment {
        // Use the SonarCloud token stored in Jenkins credentials
        SONAR_TOKEN = credentials('SONAR_TOKEN')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/sugandha-kadp/8.2CDevSecOps.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                // Allows pipeline to continue despite test failures
                sh 'npm test || true'
            }
        }

        stage('Generate Coverage Report') {
            steps {
                // Ensure coverage report exists
                sh 'npm run coverage || true'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                // This will show known CVEs in the output
                sh 'npm audit || true'
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                sh '''
                    # Download SonarScanner CLI
                    curl -sSLo sonar-scanner.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.8.0.2856-linux.zip

                    # Unzip and overwrite without prompt
                    unzip -o -q sonar-scanner.zip

                    # Add SonarScanner to PATH
                    export PATH=$PATH:$(pwd)/sonar-scanner-4.8.0.2856-linux/bin

                    # Run SonarCloud scan
                    sonar-scanner -Dsonar.login=$SONAR_TOKEN
                '''
            }
        }
    }
}