pipeline {
    agent any
    parameters {
        string(name: 'IMAGE_FRONTEND_NAME', defaultValue: 'frontend-image', description: 'The name of my frontend image')
        string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'The tag of my frontend image')
        string(name: 'CONTAINER_NAME', defaultValue: 'fontend-container', description: 'The name of my frontend container')
        string(name: 'DOCKERHUB_USER', defaultValue: 'franklinfoko', description: 'The username of docker hub')
        string(name: 'ACCOUNT_ID', defaultValue: '891377281461', description: 'The ID of AWS account')
        string(name: 'AWS_REGION', defaultValue: 'ca-central-1', description: 'Mys AWS Region')
        
    }
    
    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    sh """
                        echo Build Docker Image
                        docker build -t ${params.IMAGE_FRONTEND_NAME}:${params.IMAGE_TAG} AWS/project/python-three-tier-app/frontend/
                        docker images            
                    """
                }
                
            }
        }
        stage("Test d'acceptance") {
            steps {
                script {
                    sh """
                        echo Test acceptance

                        docker run -d --name ${params.CONTAINER_NAME} ${params.IMAGE_FRONTEND_NAME}:${params.IMAGE_TAG}
                        sleep 5
                        export IP_CONTAINER=\$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' ${params.CONTAINER_NAME})
                        curl -I http://\$IP_CONTAINER:5000

                        docker rm -f ${params.CONTAINER_NAME}
                    """
                }
            }
        }
        stage("Push Docker Image on DockerHub") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', passwordVariable: 'DOCKERHUB_PASSWORD', usernameVariable: 'DOCKERHUB_USERNAME')]) {
                    script {
                    sh """
                        echo Push Docker Image
                        echo $DOCKERHUB_PASSWORD | docker login -u $DOCKERHUB_USERNAME --password-stdin
                        docker tag ${params.IMAGE_FRONTEND_NAME}:${params.IMAGE_TAG} ${params.DOCKERHUB_USER}/${params.IMAGE_FRONTEND_NAME}:${params.IMAGE_TAG}
                        docker push ${params.DOCKERHUB_USER}/${params.IMAGE_FRONTEND_NAME}:${params.IMAGE_TAG}
                        docker logout
                    """
                    }   
                }
            }
        }
        stage("Push Docker Image on ECR") {
            steps {
                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws-credentials', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    script {
                        sh """
                            aws ecr get-login-password --region ${params.AWS_REGION} | docker login --username AWS --password-stdin ${params.ACCOUNT_ID}.dkr.ecr.${params.AWS_REGION}.amazonaws.com
                            docker tag ${params.IMAGE_FRONTEND_NAME}:${params.IMAGE_TAG} ${params.ACCOUNT_ID}.dkr.ecr.${params.AWS_REGION}.amazonaws.com/my-ecr-repo:latest
                            docker push ${params.ACCOUNT_ID}.dkr.ecr.${params.AWS_REGION}.amazonaws.com/my-ecr-repo:latest
                        """
                    }
                }
            }
        }

    }
}