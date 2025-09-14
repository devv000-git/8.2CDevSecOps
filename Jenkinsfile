pipeline {
    agent any

    environment {
        APP_DIR = 'nodejs-goof'
        SCANNER_URL = 'https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.0.1.3006-linux.zip'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/devv000-git/8.2CDevSecOps.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                dir("${APP_DIR}") {
                    sh 'npm ci'
                }
            }
        }

        stage('SonarCloud Analysis') {
  steps {
    withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
      dir("${APP_DIR}") {
        sh '''
          set -eux
          command -v java >/dev/null 2>&1 || (apt-get update && apt-get install -y openjdk-17-jre unzip curl)

          rm -rf sonar-scanner scanner.zip
          curl -sSL -o scanner.zip ${SCANNER_URL}
          unzip -q scanner.zip -d sonar-scanner

          SCANNER_DIR=$(find sonar-scanner -maxdepth 1 -type d -name "sonar-scanner-*-linux" | head -n1)

          # remove bundled JRE (x86_64 only, won’t run on arm64)
          rm -rf "$SCANNER_DIR/jre"

          export JAVA_HOME="${JAVA_HOME:-/usr/lib/jvm/java-17-openjdk}"
          export PATH="$JAVA_HOME/bin:$PWD/$SCANNER_DIR/bin:$PATH"

          "$SCANNER_DIR/bin/sonar-scanner" \
            -Dsonar.projectKey=devv000-git_8.2CDevSecOps \
            -Dsonar.organization=devv000-git \
            -Dsonar.sources=. \
            -Dsonar.host.url=https://sonarcloud.io \
            -Dsonar.login=$SONAR_TOKEN \
            -Dsonar.scanner.skipJreProvisioning=true
        '''
      }
    }
  }
}
