pipeline {
    agent any
    stages {
        stage('Build') { steps { sh 'mvn clean package -DskipTests' } }
        stage('Docker Build') { steps { sh 'docker build -t myapp:${BUILD_NUMBER} .' } }
        stage('Deploy') { steps { echo 'Deploy stage (K8s, Docker Compose, etc.)' } }
    }
}