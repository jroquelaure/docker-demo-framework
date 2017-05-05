pipeline {
  agent any
  stages {
    stage('Init Workspace') {
      steps {
        parallel(
          "Init Workspace": {
            deleteDir()
            sh 'docker rmi $dockerRepo/nginx'
            
          },
          "Get sources": {
            git(url: 'https://github.com/jroquelaure/docker-lifecycle.git', branch: 'master')
            
          }
        )
      }
    }
    stage('Config environment') {
      steps {
        sh '''sed -ie 's/ubuntu:5001/$dockerRepo/g' docker-framework/DockerFile
sed -ie 's/ubuntu:5001/$params.dockerRepo/g' docker-framework/framework-test/Dockerfile'''
      }
    }
  }
  environment {
    apiKey = 'AKCp2V6wmoc2gQYBRoZGTZAggXQA2w3JxYV6ygDkmNuvK4pepSCpFXJQqrCodTVDqXTH2CSf7'
    artifactoryInstance = 'ubuntu'
    username = 'jenkins'
    dockerRepo = 'ubuntu:5001'
    xray = 'false'
    server = Artifactory.server($artifactoryInstance)
    authUrl = '$server.url/api/npm/auth'
    bowerUrl = '$server.url/api/bower/bower-dev'
    npmUrl = '$server.url/api/npm/npm-prod'
  }
}