def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
    'UNSTABLE': 'danger'
]

pipeline {
  agent any

  tools {
    jdk 'Java11'          // Must match the name configured in Jenkins under JDK installations
    maven 'Maven-3.9.6'   // Must match the name you configured for Maven in Global Tool Configuration
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Validate Project') {
      steps {
        sh 'mvn validate'
      }
    }

    stage('Unit Test') {
      steps {
        sh 'mvn test'
      }
    }

    stage('App Packaging | Build') {
      steps {
        sh 'mvn package'
      }
    }

    stage('Integration Test') {
      steps {
        sh 'mvn verify -DskipUnitTests'
      }
    }

    stage('Checkstyle Code Analysis') {
      steps {
        sh 'mvn checkstyle:checkstyle'
      }
    }

    stage('SonarQube Inspection') {
      steps {
        sh  """
          mvn sonar:sonar \
            -Dsonar.projectKey=webapp-java \
            -Dsonar.host.url=http://172.31.12.163:9000 \
            -Dsonar.login=2883328c718b8abc3e9c2b01a07e81ff6c4be33a
          """
      }
    }

    stage("Upload Artifact To Nexus") {
      steps {
        sh 'mvn deploy'
      }
      post {
        success {
          echo 'Successfully Uploaded Artifact to Nexus Artifactory'
        }
      }
    }
  }

  post {
    always {
      echo 'Slack Notifications.'
      slackSend channel: '#af-cicd-pipeline-channel',
        color: COLOR_MAP[currentBuild.currentResult],
        message: "*${currentBuild.currentResult}:* Job Name '${env.JOB_NAME}' build ${env.BUILD_NUMBER} \n Build Timestamp: ${env.BUILD_TIMESTAMP} \n Project Workspace: ${env.WORKSPACE} \n More info at: ${env.BUILD_URL}"
    }
  }
}
