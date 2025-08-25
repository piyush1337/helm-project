pipeline {
    agent any
    environment {
        registryCredential = 'ecr:us-east-1:awscreds'
        appRegistry = "339566881566.dkr.ecr.us-east-1.amazonaws.com/vprofileappimg"
        vprofileRegistry = "https://339566881566.dkr.ecr.us-east-1.amazonaws.com"
    }
  stages {
    stage('Build App Image') {
       steps {
         script {
                dockerImage = docker.build( appRegistry + ":V$BUILD_NUMBER")
             }
        }
    }
    stage('Upload App Image') {
          steps{
            script {
              docker.withRegistry( vprofileRegistry, registryCredential ) {
                dockerImage.push("$BUILD_NUMBER")
                dockerImage.push('latest')
              }
            }
        }
    }
    stage('Remove Unused docker image') {
          steps{
            sh "docker rmi $appRegistry:V$BUILD_NUMBER"
        }
    } 
    stage('Kubernetes Deploy') {
            steps {
              sh "helm upgrade --install --force vprofile-stack helm/vprofilecharts --set appimage=${appRegistry}:V${BUILD_NUMBER} --namespace prod"
            }
        }
  }
}
