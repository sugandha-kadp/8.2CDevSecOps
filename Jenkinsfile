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
          # Download SonarScanner CLI
          curl -sSLo sonar-scanner.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.8.0.2856-linux.zip
          unzip -q sonar-scanner.zip
          export PATH=$PATH:$(pwd)/sonar-scanner-4.8.0.2856-linux/bin
          
          # Run SonarCloud scan
          sonar-scanner -Dsonar.login=$SONAR_TOKEN
        '''
      }
    }
  } 
}
