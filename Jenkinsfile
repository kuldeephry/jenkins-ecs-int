pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="517167614182"
        AWS_DEFAULT_REGION="us-west-1" 
	CLUSTER_NAME="default"
	SERVICE_NAME="nodejs-container-service"
	TASK_DEFINITION_NAME="first-run-task-definition"
	DESIRED_COUNT="1"
        IMAGE_REPO_NAME="hello-world"
        IMAGE_TAG="${env.BUILD_ID}"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
	registryCredential = "cad426a7-741b-499c-9dad-046ad3a834fb"
    }
   
    stages {

    // Tests
   //stage('Unit Tests') {
   //   steps{
   //     script {
   //       sh 'npm install'
	//  sh 'npm test -- --watchAll=false'
   //     }
     // }
   // }
        
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
			sh './script.sh'
                }
            } 
        }
      }      
      
    }
}

