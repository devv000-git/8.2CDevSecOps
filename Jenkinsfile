pipeline {
  agent any
  triggers { pollSCM('H/2 * * * *') }   // auto-run when you push changes
  stages {
    stage('Stage 1: Build') { steps { echo 'Task: Build | Tool: Maven' } }
    stage('Stage 2: Unit & Integration Tests') { steps { echo 'Task: Tests | Tools: Jest/Mocha' } }
    stage('Stage 3: Code Analysis') { steps { echo 'Task: Static analysis | Tool: ESLint' } }
    stage('Stage 4: Security Scan') { steps { echo 'Task: Security scan | Tool: npm audit/Snyk' } }
    stage('Stage 5: Deploy to Staging') { steps { echo 'Task: Deploy staging | Tool: SSH/Ansible' } }
    stage('Stage 6: Integration Tests on Staging') { steps { echo 'Task: Test staging | Tools: Jest/Mocha' } }
    stage('Stage 7: Deploy to Production') { steps { echo 'Task: Deploy prod | Tool: SSH/Ansible' } }
  }
}

