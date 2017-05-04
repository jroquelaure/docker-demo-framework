pipeline {
  agent any
  stages {
    stage('Init Workspace') {
      steps {
        deleteDir()
        sh 'docker rmi $dockerRepo/nginx'
      }
    }
  }
  environment {
    apiKey = 'AKCp2V6wmoc2gQYBRoZGTZAggXQA2w3JxYV6ygDkmNuvK4pepSCpFXJQqrCodTVDqXTH2CSf7'
    artifactoryInstance = 'ubuntu'
    artifactoryUserName = 'jenkins'
    dockerRepo = 'ubuntu:5001'
    xray = 'false'
  }
}