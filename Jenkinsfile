pipeline {
  agent any
  options { timestamps() }
  environment { EMAIL_TO = 'harshaboppi0426@gmail.com' }

  stages {
    stage('Checkout') {
      steps {
        echo 'Checking out source…'
        bat '''
          if exist logs rmdir /s /q logs
          mkdir logs
        '''
        checkout scm
      }
    }

    stage('Build') {
      steps {
        echo 'Simulated build…'
        bat '''
          echo ==== BUILD START ==== > logs\\build.log 2>&1
          echo Building (simulated)… >> logs\\build.log 2>&1
          echo ==== BUILD END ==== >> logs\\build.log 2>&1
        '''
      }
      post { always { archiveArtifacts artifacts: 'logs/build.log', allowEmptyArchive: true } }
    }

    stage('Test') {
      steps {
        echo 'Running tests… (simulated)'
        bat '''
          echo ==== TEST START ==== > logs\\test.log 2>&1
          echo Running basic simulated tests... >> logs\\test.log 2>&1
          echo All tests passed successfully! >> logs\\test.log 2>&1
          echo ==== TEST END ==== >> logs\\test.log 2>&1
        '''
      }
      post {
        success {
          emailext(
            to: "${env.EMAIL_TO}",
            subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER} — Test Stage",
            body: "Stage: TEST\nStatus: SUCCESS\nConsole: ${env.BUILD_URL}\n",
            attachmentsPattern: 'logs/test.log'
          )
        }
        failure {
          emailext(
            to: "${env.EMAIL_TO}",
            subject: "FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER} — Test Stage",
            body: "Stage: TEST\nStatus: FAILURE\nConsole: ${env.BUILD_URL}\n",
            attachmentsPattern: 'logs/test.log'
          )
        }
        always { archiveArtifacts artifacts: 'logs/test.log', allowEmptyArchive: true }
      }
    }

    stage('Security Scan') {
      steps {
        echo 'Security scan… (simulated)'
        bat '''
          echo ==== SECURITY SCAN START ==== > logs\\security.log 2>&1
          echo Simulated scan OK. >> logs\\security.log 2>&1
          echo ==== SECURITY SCAN END ==== >> logs\\security.log 2>&1
        '''
      }
      post {
        success {
          emailext(
            to: "${env.EMAIL_TO}",
            subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER} — Security Scan",
            body: "Stage: SECURITY SCAN\nStatus: SUCCESS\nConsole: ${env.BUILD_URL}\n",
            attachmentsPattern: 'logs/security.log'
          )
        }
        failure {
          emailext(
            to: "${env.EMAIL_TO}",
            subject: "FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER} — Security Scan",
            body: "Stage: SECURITY SCAN\nStatus: FAILURE\nConsole: ${env.BUILD_URL}\n",
            attachmentsPattern: 'logs/security.log'
          )
        }
        always { archiveArtifacts artifacts: 'logs/security.log', allowEmptyArchive: true }
      }
    }
  }

  post { always { echo "Pipeline finished with status: ${currentBuild.currentResult}" } }
}
