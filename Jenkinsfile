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
    server = Artifactory.server($artifactoryInstance)
    username = $artifactoryUserName
    password = $apiKey
    authUrl = "$server.url/api/npm/auth"
    bowerUrl = "$server.url/api/bower/bower-dev"
    npmUrl = "$server.url/api/npm/npm-prod"
  }
}