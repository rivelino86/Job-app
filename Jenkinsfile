pipeline {
    agent any

    parameters {
        string(name: 'VERSION', defaultValue: '1.2.1', description: 'App version to deploy')
    }

    environment {
        REPO_URL_NAME = '655040006853.dkr.ecr.us-east-1.amazonaws.com' // This variable refers to the link of the ECR repository
        ECR_NAME = 'job-app' // This variable is used to give the name of my image in ECR
        CRED_ECR = 'ecr:us-east-1:b1e04467-d055-4b14-a3ad-79ccb2653ec0' // This variable refers to my AWS credential
        FULL_REPO_URL = "https://${REPO_URL_NAME}"
        CLUSTER_NAME = 'job-cluster' // My ECS cluster name
        SERVICE_NAME = 'job-service' // My ECS service name
        SONAR_SCANNER = tool 'Sonar-scanner'
    }

    stages {
        stage("Sonar Scanner") {
            steps {
                script {
                  withSonarQubeEnv(credentialsId: 'Sonar_cred') {
  
                    sh '''
                        echo '************Sonar is ready to scan the code0***********' \
                        -Dsonar.projectKey=Job-app \
                        // -Dsonar.sources=. \
                        // -Dsonar.projectName=Job-app 
                        '''
                    }
                }
            }
        }

        stage('Scan Files with Trivy') {
            steps {
                sh "trivy fs --format table -o job-app-scan-report.html ."
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: CRED_ECR, url: FULL_REPO_URL) {
                        echo "Building Docker image and pushing to ECR"
                        sh '''
                        docker build -t job-app:${VERSION} .
                        docker tag job-app:${VERSION} ${REPO_URL_NAME}/${ECR_NAME}:${VERSION}
                        docker tag job-app:${VERSION} ${REPO_URL_NAME}/${ECR_NAME}:latest
                        docker push ${REPO_URL_NAME}/${ECR_NAME}:${VERSION}
                        docker push ${REPO_URL_NAME}/${ECR_NAME}:latest
                        '''
                    }
                }
            }
        }

        stage("Install Trivy") {
            steps {
                sh '''
                curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -
                chmod +x ./bin/trivy
                sudo mv ./bin/trivy /usr/local/bin/trivy
                '''
            }
        }

        stage('Scan Docker Image with Trivy') {
            steps {
                echo "Scanning Docker image with Trivy"
                sh "trivy image --format table -o docker_image_scan_report_${VERSION}.html ${REPO_URL_NAME}/${ECR_NAME}:${VERSION}"
            }
        }

        stage('Update ECS') {
            steps {
                sh "aws ecs update-service --cluster ${CLUSTER_NAME} --service ${SERVICE_NAME} --force-new-deployment"
            }
        }
    }
}