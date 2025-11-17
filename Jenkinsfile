pipeline {
    agent{label 'onprem-agent'}

    environment {
        DOCKER_IMAGE   = ""
        CONTAINER_NAME = ""
        DOCKER_NETWORK = ""
        CONTAINER_PORT = ""           
        HOST_PORT      = ""             
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'Develop',
                    credentialsId: '',
                    url: 'https://github.com'
            }
        }

        stage('Cleanup Old Containers & Images') {
            steps {
                echo 'Cleaning up old containers and images...'
                sh '''
                if [ "$(docker ps -aq -f name=${CONTAINER_NAME})" ]; then
                    echo "Stopping and removing old container..."
                    docker container stop ${CONTAINER_NAME} || true
                    docker container rm ${CONTAINER_NAME} || true
                fi

                if [ "$(docker images -q ${DOCKER_IMAGE})" ]; then
                    echo "Removing old images..."
                    docker rmi -f ${DOCKER_IMAGE} || true
                fi
                '''
            }
        }

        stage('Docker Build & Run') {
            steps {
                echo 'Building Docker image and running container...'
                sh '''
                docker image build -t ${DOCKER_IMAGE} .
                docker container run -d \
                    --network ${DOCKER_NETWORK} \
                    -e SERVER_PORT=${CONTAINER_PORT} \
                    -p ${HOST_PORT}:${CONTAINER_PORT} \
                    --name ${CONTAINER_NAME} \
                    ${DOCKER_IMAGE}
                '''
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
 
