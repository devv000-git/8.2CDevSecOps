stage('SonarCloud Analysis') {
  steps {
    withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
      dir("${APP_DIR}") {
        sh '''
          set -euxo pipefail

          # 1) Download scanner if not already present
          if [ ! -d sonar-scanner ]; then
            rm -rf sonar-scanner scanner.zip
            curl -sSL -o scanner.zip "${SCANNER_URL}"
            mkdir -p sonar-scanner
            unzip -q scanner.zip -d sonar-scanner
          fi

          # 2) Find the extracted dir (e.g. sonar-scanner/sonar-scanner-5.0.1.3006-linux)
          SCANNER_DIR="$(find sonar-scanner -maxdepth 1 -type d -name "sonar-scanner-*-linux" | head -n1)"
          test -n "$SCANNER_DIR"  # fail if empty
          chmod +x "$SCANNER_DIR/bin/sonar-scanner"

          # 3) Run analysis
          "$SCANNER_DIR/bin/sonar-scanner" \
            -Dsonar.projectKey=devv000-git_8.2CDevSecOps \
            -Dsonar.organization=devv000-git \
            -Dsonar.sources=. \
            -Dsonar.host.url=https://sonarcloud.io \
            -Dsonar.login="$SONAR_TOKEN"
        '''
      }
    }
  }
}
