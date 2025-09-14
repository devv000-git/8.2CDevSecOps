pipeline {
  agent any
  options { timestamps() }
  triggers { pollSCM('H/2 * * * *') }   
  environment {
    APP_DIR = 'nodejs-goof'            
    SCANNER_ZIP = 'sonar-scanner-cli-5.0.1.3006-linux.zip'
    SCANNER_URL = "https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/${SCANNER_ZIP}"
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/devv000-git/8.2CDevSecOps.git'
        sh "ls -la ${APP_DIR}"
      }
    }

    stage('Install Dependencies') {
      steps { dir("${APP_DIR}") { sh 'npm ci || npm install' } }
    }

    stage('Run Tests') {
      steps { dir("${APP_DIR}") { sh 'npm test || true' } }
    }

    stage('Generate Coverage') {
      steps { dir("${APP_DIR}") { sh 'npm run coverage || true' } }
    }

    stage('NPM Audit') {
      steps { dir("${APP_DIR}") { sh 'npm audit || true' } }
    }

    stage('SonarCloud Analysis') {
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          dir("${APP_DIR}") {
            sh """
              set -e
              command -v unzip >/dev/null 2>&1 || (apt-get update && apt-get install -y unzip)
              rm -rf sonar-scanner scanner.zip || true
              curl -L -o scanner.zip ${SCANNER_URL}
              unzip -q scanner.zip -d .
              export PATH="\$PWD/sonar-scanner-*/bin:\$PATH"

              sonar-scanner -Dsonar.login=${SONAR_TOKEN}
              echo '--- Sonar task info ---'
              test -f .scannerwork/report-task.txt && cat .scannerwork/report-task.txt || true
            """
          }
        }
      }
    }
  }

  post {
    success {
      echo '✅ Build finished successfully. Check SonarCloud dashboard for results.'
    }
    failure {
      echo '❌ Build failed. Check logs.'
    }
    always {
      archiveArtifacts artifacts: '**/.scannerwork/report-task.txt', onlyIfSuccessful: false
    }
  }
}
