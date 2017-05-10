pipeline {
  agent any
  stages {
    stage('Init Workspace') {
      steps {
        parallel(
          "Init Workspace": {
            deleteDir()
            sh 'docker rmi $dockerRepo/nginx || true'
            
          },
          "Get sources": {
            dir(path: 'tmp') {
              git(url: 'https://github.com/jroquelaure/docker-lifecycle.git', branch: 'master')
              sh 'mv docker-framework ..'
            }
            
            
          }
        )
      }
    }
    stage('Config environment') {
      steps {
        sh '''sed -ie "s/ubuntu:5001/${dockerRepo}/g" docker-framework/DockerFile
sed -ie "s/ubuntu:5001/${dockerRepo}/g" docker-framework/framework-test/Dockerfile'''
      }
    }
    stage('Build Base Image') {
      steps {
        script {
          def downloadSpec = """{
            "files": [
              {
                "pattern": "generic-local/java/*jdk*.tar.gz",
                "target": "docker-framework/jdk/jdk-8-linux-x64.tar.gz",
                "flat": "true"
              }
            ]
          }"""
          
          server = Artifactory.server(artifactoryInstance)
          buildInfo = server.download(downloadSpec)
          
          docker.build(dockerRepo + "/docker-framework:${env.BUILD_ID}", "-f docker-framework/DockerFile ./docker-framework/")
        }
        
      }
    }
    stage('Push image to Artifactory') {
      steps {
        script {
          def artDocker = Artifactory.docker(username, apiKey)
          
          def dockerInfo = artDocker.push("${dockerRepo}/docker-framework:${env.BUILD_ID}", 'docker-dev-local')
          
          buildInfo.append(dockerInfo)
          
          server.publishBuildInfo(buildInfo)
        }
        sh("""curl -H 'X-JFrog-Art-Api:${apiKey}' $server.url/api/docker/docker-dev-local/v2/promote -H "Content-Type:application/json" -d '{"targetRepo" : "docker-dev-local", "dockerRepository" : "docker-framework", "tag" : "${env.BUILD_ID}", "targetTag" : "latest", "copy": true }' """)
      }
    }
    stage('Test Image') {
      steps {
        script {
          def curlstr="curl -H 'X-JFrog-Art-Api:"+password+"' '${server}.url"
          def jarverstr = curlstr+ "/api/search/latestVersion?g=com.jfrog&a=frogsws&repos=libs-release'"
          
          sh jarverstr +' > docker-app/jar/version.txt'
          sh 'cat docker-app/jar/version.txt'
          env.JARVER=readFile('docker-app/jar/version.txt')
          
          sh "curl -S -u$username:$password $authUrl > .npmrc"
          
          sh("""echo '{
            "directory": "app/bower_components",
            "registry" : "http://$username:$password@$server.url/artifactory/api/bower/bower-dev",
            "resolvers" : [ "bower-art-resolver" ]
          }' > .bowerrc""".toString())
          
          def downloadSpec = """{
            "files": [
              {
                "pattern": "libs-release/com/jfrog/frogsws/$env.JARVER/frogsws-$env.JARVER*.jar",
                "target": "docker-framework/framework-test/jar/frogsws.jar",
                "flat":"true"
              }
            ]
          }"""
          server.download(downloadSpec)
          
          sh "mkdir -p docker-framework/framework-test/ui"
          sh "curl -u$username:$password \"$server.url/npm-prod/org/jfrog/frogsui/frogsui-\\[RELEASE\\].tgz\" -o docker-framework/framework-test/ui/frogsui.tgz"
          
          sh "tar -xvzf docker-framework/framework-test/ui/frogsui.tgz -C docker-framework/framework-test/ui/"
          sh "mv docker-framework/framework-test/ui/package docker-framework/framework-test/ui/frogsui"
          //build framework_test image
          
          testApp = docker.build("framework-test:$buildInfo.number", "-f docker-framework/framework-test/Dockerfile ./docker-framework/framework-test/")
          //run it
          testApp.withRun('-p 8099:8000 -p 9000:9000') { c ->
          sleep 10
          sh 'curl "localhost:8099/frogsui/app/index.html"'
        }
        sh "docker rmi " + dockerRepo + "/docker-framework:$buildInfo.number"
        sh "docker rmi " + dockerRepo + "/docker-framework:latest"
      }
      
    }
  }
}
environment {
  apiKey = 'AKCp2V6wmoc2gQYBRoZGTZAggXQA2w3JxYV6ygDkmNuvK4pepSCpFXJQqrCodTVDqXTH2CSf7'
  artifactoryInstance = 'ubuntu'
  username = 'jenkins'
  dockerRepo = 'ubuntu:5001'
  xray = 'false'
  authUrl = '$server.url/api/npm/auth'
  bowerUrl = '$server.url/api/bower/bower-dev'
  npmUrl = '$server.url/api/npm/npm-prod'
  server = 'Artifactory.server($artifactoryInstance)'
  buildInfo = ''
}
}