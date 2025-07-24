def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
    'UNSTABLE': 'danger'
]
pipeline {
  agent any // This specifies that the pipeline can run on any available agent
  stages {
    stage('Validate Project') {
        steps {
            sh 'mvn validate'
        }
    }
    stage('Unit Test'){
        steps {
            sh 'mvn test'
        }
    }
    stage('App Packaging | Build'){
        steps {
            sh 'mvn package'
        }
    }
    stage('Integration Test'){
        steps {
            sh 'mvn verify -DskipUnitTests'
        }
    }
    stage ('Checkstyle Code Analysis'){
        steps {
            sh 'mvn checkstyle:checkstyle'
        }
    }
    stage('SonarQube Inspection') {
        steps {
            sh  """mvn sonar:sonar \
                   -Dsonar.projectKey=Java-WebApp-Project \
                   -Dsonar.host.url=http://172.31.1.99:9000 \
                   -Dsonar.login=1981b1e7b8f67fa325b90171b51d0d3f621a1fba"""
        }
    } 
    stage("Upload Artifact To Nexus"){
        steps{
             sh 'mvn deploy'
        } 
        post {
            success {
              echo 'Successfully Uploaded Artifact to Nexus Artifactory'
        }
      }
    }
  }
}
