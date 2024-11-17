pipeline{
    agent any
     
     parameters {
        string(name: 'VERSION', defaultValue: '1.2.1', description: 'App to deploy')
    }
     environment{
          REPO_URL_NAME = '655040006853.dkr.ecr.us-east-1.amazonaws.com'
          APP_NAME ='job-app'
          ECR_APP_NAME = 'find-job'
          CRED_ECR = 'ecr:us-east-1:b1e04467-d055-4b14-a3ad-79ccb2653ec0'
        FULL_REPO_URL = 'https://${REPO_URL_NAME}'
     }
    stages{
        stage('build image'){
            steps{
                script{
                       withDockerRegistry(credentialsId: CRED_ECR, url: FULL_REPO_URL){
                        echo "======= How can i help you ??? ========"
                        sh "docker build -t ${APP_NAME}:${params.VERSION} ."
                        sh "docker tag ${APP_NAME}:${params.VERSION} ${REPO_URL_NAME}/${ECR_APP_NAME}:${params.VERSION}"
                        sh "docker tag ${APP_NAME}:${params.VERSION} ${REPO_URL_NAME}/${ECR_APP_NAME}:$latest"
                        sh "docker push ${REPO_URL_NAME}/${ECR_APP_NAME}:${params.VERSION}"
                        sh "docker push ${REPO_URL_NAME}/${ECR_APP_NAME}:latest"
                 }
                }
             }
            }
        }
     }
    