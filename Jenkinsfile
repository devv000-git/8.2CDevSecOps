pipeline {
    agent any

    environment {
        APP_DIR = 'nodejs-goof'
        SCANNER_URL = 'https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.0.1.3006-linux.zip'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/devv000-git/8.2CDevSecOps.git'
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
                            ./sonar-scanner/bin/sonar-scanner \
                            -Dsonar.projectKey=devv000-git_8.2CDevSecOps \
                            -Dsonar.organization=devv000-git \
                            -Dsonar.sources=. \
                            -Dsonar.host.url=https://sonarcloud.io \
                            -Dsonar.login=$SONAR_TOKEN
                        '''
                    }
                }
            }
        }
    }
}
