pipeline {
    agent any

     environment{
       registryCredential = 'ecr:<zone>:<CredentialID>'
       appRegistry = "<Registry Name>"
       capstoneRegistry = "<Registry Url>"
       cluster = "<Cluster Name>"
        service = "<Service Name>"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/Aryanhac/Capstone-Project'
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
            withAWS(credentials: '<CredentialID>', region: 'us-east-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
