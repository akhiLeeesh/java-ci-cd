pipeline {
    agent {
        docker {
            image 'maven:3.8.4-openjdk-17'  // Maven + Java environment
            args '-v /var/run/docker.sock:/var/run/docker.sock'  // Required to run docker commands
        }
    }

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('dockerhub-creds')
        DOCKER_HUB_USERNAME = "${DOCKER_HUB_CREDENTIALS_USR}"
        DOCKER_HUB_PASSWORD = "${DOCKER_HUB_CREDENTIALS_PSW}"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/akhileeesh/java-ci-cd.git'
            }
        }

        stage('Build Java Services') {
            steps {
                sh '''
                cd discovery-service && mvn clean package
                cd ../api-gateway && mvn clean package
                cd ../product-service && mvn clean package
                cd ../order-service && mvn clean package
                '''
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                docker build -t ${DOCKER_HUB_USERNAME}/discovery-service:${IMAGE_TAG} discovery-service/
                docker build -t ${DOCKER_HUB_USERNAME}/api-gateway:${IMAGE_TAG} api-gateway/
                docker build -t ${DOCKER_HUB_USERNAME}/product-service:${IMAGE_TAG} product-service/
                docker build -t ${DOCKER_HUB_USERNAME}/order-service:${IMAGE_TAG} order-service/
                '''
            }
        }

        stage('Push Docker Images') {
            steps {
                sh '''
                echo $DOCKER_HUB_PASSWORD | docker login -u $DOCKER_HUB_USERNAME --password-stdin
                docker push ${DOCKER_HUB_USERNAME}/discovery-service:${IMAGE_TAG}
                docker push ${DOCKER_HUB_USERNAME}/api-gateway:${IMAGE_TAG}
                docker push ${DOCKER_HUB_USERNAME}/product-service:${IMAGE_TAG}
                docker push ${DOCKER_HUB_USERNAME}/order-service:${IMAGE_TAG}
                docker logout
                '''
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                sh '''
                docker-compose down
                docker-compose up -d
                '''
            }
        }
    }

    post {
        always { echo "Pipeline finished" }
        success { echo "Build & deployment successful!" }
        failure { echo "Pipeline failed!" }
    }
}
