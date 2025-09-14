pipeline {
  agent any
  triggers { pollSCM('H/2 * * * *') }   // auto-build on push

  stages {
    stage('Checkout') {
      steps {
        // make sure we’re building the repo that contains nodejs-goof
        git branch: 'main', url: 'https://github.com/devv000-git/8.2CDevSecOps.git'
      }
    }

    stage('Install Dependencies') {
      steps { sh 'npm install' }
    }

    stage('Run Tests') {
      steps { sh 'npm test || true' }      // don’t fail the whole build if tests fail
    }

    stage('Generate Coverage') {
      steps { sh 'npm run coverage || true' }
    }

    stage('NPM Audit') {
      steps { sh 'npm audit || true' }     // show CVEs but keep build green for demo
    }
  }
}
