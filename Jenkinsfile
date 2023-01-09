pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="939612840870"
        AWS_DEFAULT_REGION="ap-southeast-1"
        CLUSTER_NAME="one2onetool-cluster"
	SERVICE_NAME="one2onetool-container-service"
	TASK_DEFINITION_NAME="first-run-task-definition"
	DESIRED_COUNT="1"
        IMAGE_REPO_NAME="one2onetool"
        IMAGE_TAG="${env.BUILD_ID}"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
	registryCredential = "admin-user"
    }
   
    stages {

    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
     steps{  
         script {
			docker.withRegistry("https://" + REPOSITORY_URI, "ecr:${AWS_DEFAULT_REGION}:" + registryCredential) {
                    	dockerImage.push()
                	}
         }
        }
      }
      
    stage('Deploy') {
     steps{
            withAWS(credentials: registryCredential, region: "${AWS_DEFAULT_REGION}") {
                script {
			sh "chmod +x -R ${env.WORKSPACE}"
			sh './script.sh'
                }
            } 
        }
      }      
      
    }
}
