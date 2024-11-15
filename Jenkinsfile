pipeline{
    agent any
     
     parameters {
        string(name: 'VERSION', defaultValue: '1.0.0', description: 'App to deploy')
    }

    stages{
        stage('Testing'){
            steps{
             script{
                        withDockerRegistry(credentialsId: 'ecr:us-east-1:Job-id', url: 'https://655040006853.dkr.ecr.us-east-1.amazonaws.com/'){
                        echo "======= How can i help you ??? ========"
                        sh "docker build -t job-app:${param.VERSION} ."
              
                }
            }
        }
     }
 }
}
