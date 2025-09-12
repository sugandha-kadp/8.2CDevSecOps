pipeline { 
  agent any 

  environment {
      SONAR_TOKEN = credentials('SONAR_TOKEN') // Use the SonarCloud token stored in Jenkins credentials
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
        sh 'npm test || true' // Allows pipeline to continue despite test failures 
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
        sh 'npm audit || true' // This will show known CVEs in the output 
      } 
    } 

    stage('SonarCloud Analysis') {
        steps {
            sh '''
                # Remove existing SonarScanner directory if it exists
                rm -rf sonar-scanner-4.8.0.2856-linux

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
