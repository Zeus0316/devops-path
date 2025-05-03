pipeline {
    agent any
    parameters {
        string(name: 'IMAGE_FRONTEND_NAME', defaultValue: 'frontend-image', description: 'The name of my frontend image')
        string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'The tag of my frontend image')
        string(name: 'CONTAINER_NAME', defaultValue: 'fontend-container', description: 'The nameof my frontend container')
        
    }
    
    stages {
        stage('Build Docker Image') {
            steps {
                echo 'Build Docker Image'
                docker build -t ${params.IMAGE_FRONTEND_NAME}:${params.IMAGE_TAG} devops-path/AWS/project/python-three-tier-app/frontend/
                deocker images
            }
        }
        // stage("Test d'acceptance") {
        //     steps {
        //         echo "Test d'acceptance"

        //         docker run -d --name frontend-container ${IMAGE_FRONTEND_NAME}:${IMAGE_TAG}
        //         sleep 5
        //         export IP_CONTAINER=$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' ${CONTAINER_NAME})
        //         curl -I http://$IP_CONTAINER:5000

        //         docker rm -f frontend-container
        //         docker tag ${IMAGE_FRONTEND_NAME}:${IMAGE_TAG} franklinfoko/${IMAGE_FRONTEND_NAME}:${IMAGE_TAG}

        //         docker images
        //     }
        // }
    }
}