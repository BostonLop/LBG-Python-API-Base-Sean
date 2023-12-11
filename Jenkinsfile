pipeline {
    agent any
    stages {
        stage('Init') {
            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.156.0.2 << EOF
                docker stop flask-app || echo "flask-app not running!"
                docker rm flask-app || echo "flask-app not running!"
                docker stop nginx || echo "nginx not running!"
                docker rm nginx || echo "nginx not running!"
                docker rmi s90hef/python-api || echo "Image does not exist!"
                docker rmi s90hef/flask-nginx || echo "Image does not exist!"
                docker network create WK2-Project || echo "network already exists"
                '''
           }
        }
        stage('Build') { 
            steps {
                sh '''
                docker build -t s90hef/python-api -t s90hef/python-api:v${BUILD_NUMBER} .
                docker build -t s90hef/flask-nginx -t s90hef/flask-nginx:v${BUILD_NUMBER} ./nginx
                '''
           }
        }
        stage('Push') {
            steps {
                sh '''
                docker push s90hef/python-api
                docker push s90hef/python-api:v${BUILD_NUMBER}
                docker push s90hef/flask-nginx
                docker push s90hef/flask-nginx:v${BUILD_NUMBER}
                '''
           }
        }
        stage('Deploy') {
            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.156.0.2 << EOF
                docker run -d --name flask-app --network WK2-Project s90hef/python-api
                docker run -d -p 80:80 --name nginx --network WK2-Project s90hef/flask-nginx
                '''
            }
        }
        stage('Clean-up') {
            steps {
                sh '''
                docker system prune -f
                docker rmi s90hef/python-api:v${BUILD_NUMBER}
                docker rmi s90hef/flask-nginx:v${BUILD_NUMBER}
                '''
           }
        }
    }
}
