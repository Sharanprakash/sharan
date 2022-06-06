pipeline {
     agent any
     environment {
        //once you sign up for Docker hub, use that user_id here
        registry = "gcr.io/genuine-range-349802/"
        //- update your credentials ID after creating credentials for connecting to GCR
        registryCredential = 'genuine-range-349802'
        dockerImage = ""
    }
    stages {

        stage ('checkout') {
            steps {
            checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'github', url: 'https://github.com/Sharanprakash/sharan.git']]])
            }
        }
       
        stage ('Build docker image') {
            steps {
                script {
                dockerImage = docker.build registry
                }
            }
        }
      
         // Uploading Docker images into GCR
       stage('Upload Image') {
           steps{   
              script {
               docker.withRegistry( '', registryCredential ) {
               dockerImage.push()
               dockerImage.push('latest')
            }
        }
      }
    }
   
      stage ('K8S Deploy') {
           steps {
            script {
                kubernetesDeploy(
                    configs: 'k8s.yaml',
                    kubeconfigId: 'k8s',
                    enableConfigSubstitution: true
                    )           
               
            }
        }
    }
  
    }  
}



