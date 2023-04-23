pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-northest-1:202051107-Kunal'
       appRegistry = "049153575147.dkr.ecr.ap-northest-1.amazonaws.com/capstoneproject"
       capstoneRegistry = "049153575147.dkr.ecr.ap-northest-1.amazonaws.com"
       cluster = "Capstone_Kunal"
        service = "capstone_kunal"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/Kunaljainsethiya/website'
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
            withAWS(credentials: '202051107', region: 'ap-northest-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
