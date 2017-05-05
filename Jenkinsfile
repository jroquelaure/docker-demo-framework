pipeline {
  agent any
  stages {
    stage('Init Workspace') {
      steps {
        deleteDir()
        sh 'docker rmi $dockerRepo/nginx'
        def server = Artifactory.server($artifactoryInstance)
        def username = $artifactoryUserName
        def password = $apiKey
        def authUrl = "$server.url/api/npm/auth"
        def bowerUrl = "$server.url/api/bower/bower-dev"
        def npmUrl = "$server.url/api/npm/npm-prod"
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