pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-northeast-1:202051098'
       appRegistry = "850610700034.dkr.ecr.ap-northeast-1.amazonaws.com/capstone"
       capstoneRegistry = "http://850610700034.dkr.ecr.ap-northeast-1.amazonaws.com"
       cluster = "Capstone"
        service = "CapstoneService"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/kamalkumar7/website.git'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: '202051098', region: 'ap-northeast-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
