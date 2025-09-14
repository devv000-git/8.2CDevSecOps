pipeline {
  agent any
  triggers { pollSCM('H/2 * * * *') }   // auto-build on push (~every 2 min)

  environment {
    REPO_URL   = 'https://github.com/devv000-git/8.2CDevSecOps.git'
    APP_DIR    = 'nodejs-goof'          // set to '.' if package.json is in repo root
    SCANNER_ZIP= 'sonar-scanner-cli-5.0.1.3006-linux.zip'
    SCANNER_URL= "https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/${SCANNER_ZIP}"
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: "${env.REPO_URL}"
        sh "ls -la ${APP_DIR}"
        sh "test -f ${APP_DIR}/package.json || (echo 'ERROR: package.json not found in ${APP_DIR}. Set APP_DIR correctly.' && exit 1)"
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
        sh '''
          set -e
          command -v unzip >/dev/null 2>&1 || (apt-get update && apt-get install -y unzip)
          rm -rf sonar-scanner scanner.zip || true
          curl -L -o scanner.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.0.1.3006-linux.zip
          unzip -q scanner.zip -d sonar-scanner

          # Resolve scanner path explicitly
          SCANNER_DIR=$(find sonar-scanner -maxdepth 1 -type d -name "sonar-scanner-*")
          export PATH="$PWD/$SCANNER_DIR/bin:$PATH"

          "$SCANNER_DIR/bin/sonar-scanner" -Dsonar.login=$SONAR_TOKEN
          echo '--- Sonar task info ---'
          test -f .scannerwork/report-task.txt && cat .scannerwork/report-task.txt || true
        '''
      }
    }
  }
}
