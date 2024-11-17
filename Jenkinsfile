pipeline{
    agent any
     
     parameters {
        string(name: 'VERSION', defaultValue: '1.2.1', description: 'App to deploy')
    }
     environment{
          REPO_URL_NAME = '655040006853.dkr.ecr.us-east-1.amazonaws.com'
          //APP_NAME ='job-app'
          ECR_NAME = 'job-app'
          CRED_ECR = 'ecr:us-east-1:b1e04467-d055-4b14-a3ad-79ccb2653ec0'
        FULL_REPO_URL = "https://${REPO_URL_NAME}"
        CLUSTER_NAME = 'job-cluster'
        SERVICE_NAME = 'job-service'


     }
    stages{
        stage("build image"){
            steps{
                script{
                       withDockerRegistry(credentialsId: CRED_ECR, url: FULL_REPO_URL) {
                        echo "======= How can i help you ??? ========"
                             // Build the Docker image
                        sh "docker build -t job-app:${params.VERSION} ."
                        //     // Tag the image image for ECR
                        // sh "docker tag job-app:${params.VERSION} ${REPO_URL_NAME}/${ECR_NAME}:${params.VERSION}"
                        // sh "docker tag job-app:${params.VERSION} ${REPO_URL_NAME}/${ECR_NAME}:latest"
                        //      // Push iamge to ECR
                        // sh "docker push ${REPO_URL_NAME}/${ECR_NAME}:latest"
                        // sh "docker push ${REPO_URL_NAME}/${ECR_NAME}:${params.VERSION}"
                    }
                 }
                 stage('scan by trivy'){
                    steps{
                        sh 'trivy job-app:${params.VERSION}'
                
                    }
                 }
              }
            }
               stage("Update ECS") {
            steps {  
                    sh "aws ecs update-service --cluster ${CLUSTER_NAME} --service ${SERVICE_NAME} --force-new-deployment"
        }
     }
   }
 }
