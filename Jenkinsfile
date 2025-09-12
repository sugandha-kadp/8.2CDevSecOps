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
                withEnv([
                    'JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64',
                    'PATH+JAVA=$JAVA_HOME/bin'
                ]) {
                    sh '''
                        # Download SonarScanner if not already
                        if [ ! -d "sonar-scanner-4.8.0.2856-linux" ]; then
                            curl -sSLo sonar-scanner.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.8.0.2856-linux.zip
                            unzip -o -q sonar-scanner.zip
                        fi

                        # Use quoted path to avoid issues with spaces
                        export PATH="$PWD/sonar-scanner-4.8.0.2856-linux/bin:$PATH"

                        sonar-scanner -Dsonar.login=$SONAR_TOKEN
                    '''
                }
            }
        }

    }
}