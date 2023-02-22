pipeline {
    agent any
    stages {
        stage('Build Docker App') {
            steps {
                sh '''
                docker build -t poojasdocker2023/python-app:latest -t poojasdocker2023/python-app:build-$BUILD_NUMBER .
                '''
           }
        }
        stage('Build NGINX App') {
            steps {
                sh '''
                cd nginx
                docker build -t poojasdocker2023/nginx-pyapp-custom:latest -t poojasdocker2023/nginx-pyapp-custom:build-$BUILD_NUMBER .
                '''
           }
        }
        stage('Push Image to gcr Hub') {
            steps {
                sh '''
                docker push poojasdocker2023/python-app:latest
                docker push poojasdocker2023/python-app:build-$BUILD_NUMBER
                docker push poojasdocker2023/nginx-pyapp-custom:latest
                docker push poojasdocker2023/nginx-pyapp-custom:build-$BUILD_NUMBER
                '''
            }
        }

        stage('Deploy Application to the Cluster') {
            steps {
               sh '''
               kubectl apply -f .
               cd ./nginx
               kubectl apply -f .         
               '''
            }
        }
     }
}
