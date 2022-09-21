pipeline {
    agent { label 'TEST' } 
  environment {
    registry = "yaroslade/flask"
    registryCredential = 'yaroslade-dockerhub'
    dockerImage = ''
  }
  stages {
      stage ('Building Image') {
          steps {
              script {
          dockerImage = docker.build(registry + ":$BUILD_NUMBER")
              }
          }
      }
      stage ('Deploy Image') {
          steps {
              script {
                  docker.withRegistry( '', registryCredential ) {
                     dockerImage.push() 
                  }
              }
          }
      }
      stage ('Remove Unused docker image') {
          steps {
              sh "docker rmi $registry:$BUILD_NUMBER"
          }
      }
      stage('Invoke Job') {
      steps{
        build job: 'DeployToProduction', 
        parameters: [
            string(name: 'fl_version', value: String.valueOf("${currentBuild.getNumber()}"))
        ]
      }
    }
  }
}
