pipeline {
    agent any
    stages {
        stage('Init') {
            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.156.0.2 << EOF
                docker stop flask-app || echo "flask-app not running!"
                docker rm flask-app || echo "flask-app not running!"
                '''
           }
        }
        stage('Build') { 
            steps {
                sh '''
                docker build -t s90hef/python-api -t s90hef/python-api:v${BUILD_NUMBER} .
                '''
           }
        }
        stage('Push') {
            steps {
                sh '''
                docker push s90hef/python-api
                docker push s90hef/python-api:v${BUILD_NUMBER}
                '''
           }
        }
        stage('Deploy') {
            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.156.0.2 << EOF
                docker run -d -p 80:8080 --name flask-app s90hef/python-api
                '''
            }
        }
        stage('Clean-up') {
            steps {
                sh '''
                docker system prune -f
                '''
           }
        }
    }
}
