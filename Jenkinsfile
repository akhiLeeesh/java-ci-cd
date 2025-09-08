pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('dockerhub-creds')
        DOCKER_HUB_USERNAME = "${DOCKER_HUB_CREDENTIALS_USR}"
        DOCKER_HUB_PASSWORD = "${DOCKER_HUB_CREDENTIALS_PSW}"
        IMAGE_TAG = "${BUILD_NUMBER}" // each build has a unique tag
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
                cd backend && mvn clean package
                cd ../frontend && mvn clean package
                '''
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                docker build -t ${DOCKER_HUB_USERNAME}/backend:${IMAGE_TAG} backend/
                docker build -t ${DOCKER_HUB_USERNAME}/frontend:${IMAGE_TAG} frontend/
                '''
            }
        }

        stage('Push Docker Images to Docker Hub') {
            steps {
                sh '''
                echo $DOCKER_HUB_PASSWORD | docker login -u $DOCKER_HUB_USERNAME --password-stdin
                docker push ${DOCKER_HUB_USERNAME}/backend:${IMAGE_TAG}
                docker push ${DOCKER_HUB_USERNAME}/frontend:${IMAGE_TAG}
                docker logout
                '''
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                sh '''
                # Optional: pull new images and restart containers
                docker-compose down
                docker-compose up -d
                '''
            }
        }

    }

    post {
        always {
            echo "Pipeline finished"
        }
        success {
            echo "Build, push, and deploy successful!"
        }
        failure {
            echo "Pipeline failed. Check logs!"
        }
    }
}
