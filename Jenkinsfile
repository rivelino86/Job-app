pipeline {
    agent any

    parameters {
        string(name: 'VERSION', defaultValue: '1.2.1', description: 'App to deploy')
    }

    environment {
        REPO_URL_NAME = '655040006853.dkr.ecr.us-east-1.amazonaws.com'
        ECR_NAME = 'job-app'
        CRED_ECR = 'ecr:us-east-1:b1e04467-d055-4b14-a3ad-79ccb2653ec0'
        FULL_REPO_URL = "https://${REPO_URL_NAME}"
        CLUSTER_NAME = 'job-cluster'
        SERVICE_NAME = 'job-service'
        SONAR_SCANNER = tool 'Sonar-scanner'
    }

    stage("Sonar Scanner") {
    steps {
        script {
            withSonarQubeEnv(credentialsId: 'SonarQube') {
                sh '''
                ${SONAR_SCANNER}/bin/sonar-scanner \
                -Dsonar.projectKey=job-app \
                -Dsonar.sources=. \
                -Dsonar.projectName=job-app \
                -Dsonar.java.binaries=. \
                -Dsonar.host.url=http://<SonarQube_Server_IP>:<Port> \
                -Dsonar.login=<Authentication_Token>
                '''
            }
        }
    }
}

        stage("Scan Files with Trivy") {
            steps {
                sh "trivy fs --format table -o job-app-scan-report.html ."
            }
        }

        stage("Build and Push Docker Image") {
            steps {
                script {
                    withDockerRegistry(credentialsId: CRED_ECR, url: FULL_REPO_URL) {
                        echo "Building Docker image and pushing to ECR"
                        // Build the Docker image
                        sh "docker build -t job-app:${params.VERSION} ."
                        // Tag the image for ECR
                        sh "docker tag job-app:${params.VERSION} ${REPO_URL_NAME}/${ECR_NAME}:${params.VERSION}"
                        sh "docker tag job-app:${params.VERSION} ${REPO_URL_NAME}/${ECR_NAME}:latest"
                        // Push the image to ECR
                        sh "docker push ${REPO_URL_NAME}/${ECR_NAME}:${params.VERSION}"
                        sh "docker push ${REPO_URL_NAME}/${ECR_NAME}:latest"
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

        stage("Scan Docker Image with Trivy") {
            steps {
                echo "Scanning Docker image with Trivy"
                sh "trivy image --format table -o docker_image_scan_report_${params.VERSION}.html ${REPO_URL_NAME}/${ECR_NAME}:${params.VERSION}"
            }
        }

        // stage("Update ECS") {
        //     steps {
        //         sh "aws ecs update-service --cluster ${CLUSTER_NAME} --service ${SERVICE_NAME} --force-new-deployment"
        //     }
        // }
    
