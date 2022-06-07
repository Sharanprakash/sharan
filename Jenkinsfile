pipeline {
     agent any
     environment {
        //once you sign up for Docker hub, use that user_id here
        registry = "gcr.io/genuine-range-349802/"
        //- update your credentials ID after creating credentials for connecting to GCR
        registryCredential = "My First Project"
        IMAGE_REPO_NAME="demo"
        IMAGE_TAG="latest"
        REPOSITORY_URI = "gcr.io/genuine-range-349802/${IMAGE_REPO_NAME}"
        dockerImage = ""
        PROJECT_ID = 'genuine-range-349802'
        CLUSTER_NAME = 'genuine-range-349802-gke'
        LOCATION = 'us-west1'
        CREDENTIALS_ID = 'My First Project'
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
                dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
                }
            }
        }
        
        stage ('Gcloud auth') {
            steps {
                sh 'gcloud auth configure-docker'
            }
        }
      
       // Uploading Docker images into GCR
    stage('Pushing to GCR') {
     steps{  
         script {
                sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
                sh "docker push ${REPOSITORY_URI}:${IMAGE_TAG }"
         }
        }
      }
      
      /////////////
      
     stage('Deploy to GKE') {
            steps{
                step([
                $class: 'KubernetesEngineBuilder',
                projectId: env.PROJECT_ID,
                clusterName: env.CLUSTER_NAME,
                location: env.LOCATION,
                manifestPattern: 'k8s.yaml',
                credentialsId: env.CREDENTIALS_ID,
                verifyDeployments: true])
            }
        }
    }

}
