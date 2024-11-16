pipeline{
    agent any
     
     parameters {
        string(name: 'VERSION', defaultValue: '1.0.0', description: 'App to deploy')
    }
     environment{
          REPO_URL_NAME = '655040006853.dkr.ecr.us-east-1.amazonaws.com'
          APP_NAME ='Job-app'
          ECR_APP_NAME = 'find-job'
          CRED_ECR = 'ecr:us-east-1:Job-id'
     }
    stages{
        stage('build image'){
            steps{
                script{
                        withDockerRegistry(credentialsId: 'CRED_ECR', url: '655040006853.dkr.ecr.us-east-1.amazonaws.com/') {

                        echo "======= How can i help you ??? ========"
                        sh "docker build -t ${APP_NAME}:${param.VERSION} ."
                        sh "docker tag ${APP_NAME}:${param.VERSION} ${REPO_URL_NAME}/${ECR_APP_NAME}:${params.VERSION}"
                 }
                }
             }
            }
        }
     }
