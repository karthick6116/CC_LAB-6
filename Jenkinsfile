pipeline {
    agent any
    stages {
        stage('Build Backend Image') {
            steps {
                sh '''
                docker rmi -f backend-app || true
                docker build -t backend-app backend
                '''
            }
        }
        stage('Deploy Backend Containers') {
            steps {
              '''
                sh 'sleep 3' 
                sh 'docker network create app-network || true'
                sh 'docker rm -f backend1 backend2 || true'
                sh 'docker run -d --name backend1 --network app-network backend-app'
                sh 'docker run -d --name backend2 --network app-network backend-app'
                '''
            }
        }
        stage('Deploy NGINX Load Balancer') {
            steps {
              '''
                 sh 'sleep 2' 
                sh 'docker rm -f nginx-lb || true'
                
                sh 'docker run -d --name nginx-lb --network app-network -p 8081:80 nginx'
                
                sh 'docker cp nginx/default.conf nginx-lb:/etc/nginx/conf.d/default.conf'
                sh 'docker exec nginx-lb nginx -s reload'
                '''
            }
        }
    }
    post {
        success {
            echo 'Pipeline executed successfully. NGINX load balancer is running.'
        }
        failure {
            echo 'Pipeline failed. Check console logs for errors.'
        }
    }
}
