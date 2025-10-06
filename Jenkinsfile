pipeline {
  agent any

  options {
    timestamps()
    buildDiscarder(logRotator(numToKeepStr: '20'))
  }

  environment {
    EMAIL_TO = 'harshaboppi0426@gmail.com'
  }

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
        echo 'Running build…'
        bat '''
          echo ==== BUILD START ==== > logs\\build.log 2>&1
          call npm ci >> logs\\build.log 2>&1
          call npm run build >> logs\\build.log 2>&1
          echo ==== BUILD END ==== >> logs\\build.log 2>&1
        '''
      }
      post {
        always {
          archiveArtifacts artifacts: 'logs/build.log', allowEmptyArchive: true
        }
      }
    }

    stage('Test') {
      steps {
        echo 'Running tests…'
        bat '''
          echo ==== TEST START ==== > logs\\test.log 2>&1
          call npm test >> logs\\test.log 2>&1
          echo ==== TEST END ==== >> logs\\test.log 2>&1
        '''
      }
      post {
        success {
          emailext(
            to: "${env.EMAIL_TO}",
            subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER} — Test Stage",
            body: """\
Stage: TEST
Status: SUCCESS
Console: ${env.BUILD_URL}
""",
            attachmentsPattern: 'logs/test.log'
          )
        }
        failure {
          emailext(
            to: "${env.EMAIL_TO}",
            subject: "FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER} — Test Stage",
            body: """\
Stage: TEST
Status: FAILURE
Console: ${env.BUILD_URL}
""",
            attachmentsPattern: 'logs/test.log'
          )
        }
        always {
          archiveArtifacts artifacts: 'logs/test.log', allowEmptyArchive: true
        }
      }
    }

    stage('Security Scan') {
      steps {
        echo 'Running security scan…'
        bat '''
          echo ==== SECURITY SCAN START ==== > logs\\security.log 2>&1
          call npm audit --json > logs\\security.json 2>> logs\\security.log
          type logs\\security.json >> logs\\security.log
          echo ==== SECURITY SCAN END ==== >> logs\\security.log 2>&1
        '''
      }
      post {
        success {
          emailext(
            to: "${env.EMAIL_TO}",
            subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER} — Security Scan",
            body: """\
Stage: SECURITY SCAN
Status: SUCCESS
Console: ${env.BUILD_URL}
""",
            attachmentsPattern: 'logs/security.log'
          )
        }
        failure {
          emailext(
            to: "${env.EMAIL_TO}",
            subject: "FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER} — Security Scan",
            body: """\
Stage: SECURITY SCAN
Status: FAILURE
Console: ${env.BUILD_URL}
""",
            attachmentsPattern: 'logs/security.log'
          )
        }
        always {
          archiveArtifacts artifacts: 'logs/security.log,logs/security.json', allowEmptyArchive: true
        }
      }
    }
  }

  post {
    always {
      echo "Pipeline finished with status: ${currentBuild.currentResult}"
    }
  }
}
