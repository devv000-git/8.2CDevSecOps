pipeline {
  agent any
  triggers { pollSCM('H/2 * * * *') }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/devv000-git/8.2CDevSecOps.git'
      }
    }

    stage('Install Dependencies') {
      steps { dir('nodejs-goof') { sh 'npm install' } }
    }
    stage('Run Tests') {
      steps { dir('nodejs-goof') { sh 'npm test || true' } }
    }
    stage('Generate Coverage') {
      steps { dir('nodejs-goof') { sh 'npm run coverage || true' } }
    }
    stage('NPM Audit') {
      steps { dir('nodejs-goof') { sh 'npm audit || true' } }
    }
  }
}
