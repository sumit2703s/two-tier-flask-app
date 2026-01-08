pipeline {
    agent any

    environment {
        IMAGE_NAME = "sumit2703/flask-app-image"
        TAG = "latest"
    }
    stages {

        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/sumit2703s/two-tier-flask-app.git', branch: "main"
            }
        }
        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
            credentialsId: 'dockerHubCreds',
            usernameVariable: 'DOCKER_USER',
            passwordVariable: 'DOCKER_PASS'
        )]) {
            sh '''
            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
            '''
        }
            }
        }
        stage('Docker Build') {
            steps {
                sh "docker build -t $IMAGE_NAME:$TAG ."
            }
        }
        stage('Trivy File System Scan'){
            steps {
                sh "trivy fs . -o reports.json"
            }
        }
        stage('Docker Push') {
            steps {
                sh "docker push $IMAGE_NAME:$TAG"
            }
        }

        stage('Deploy using Docker Compose') {
            steps {
                sh "docker compose up -d --build"
            }
        }
    }
    post{
        success{
            script{
                emailext body: 'Two Tier Flask App Build Successful',
                subject: 'Build Success',
                to: 'gaxoriv144@cucadas.com'
            }
        }
        failure{
            script{
                emailext body: 'Two Tier Flask App Build Failed',
                subject: 'Build Failed',
                to: 'gaxoriv144@cucadas.com'
            }
        }
    }
}
