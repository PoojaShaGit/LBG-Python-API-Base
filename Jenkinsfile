pipeline {
    agent any
    stages {
        stage('Build Docker App') {
            steps {
                sh '''
                docker build -t eu.gcr.io/lbg-cloud-incubation/python-app:latest -t eu.gcr.io/lbg-cloud-incubation/python-app:build-$BUILD_NUMBER .
                '''
           }
        }
        stage('Build NGINX App') {
            steps {
                sh '''
                cd nginx
                docker build -t eu.gcr.io/lbg-cloud-incubation/nginx-pyapp-custom:latest -t eu.gcr.io/lbg-cloud-incubation/nginx-pyapp-custom:build-$BUILD_NUMBER .
                '''
           }
        }
        stage('Push Image to Hub') {
            steps {
                sh '''
                docker push eu.gcr.io/lbg-cloud-incubation/python-app:latest
                docker push eu.gcr.io/lbg-cloud-incubation/python-app:build-$BUILD_NUMBER
                docker push eu.gcr.io/lbg-cloud-incubation/nginx-pyapp-custom:latest
                docker push eu.gcr.io/lbg-cloud-incubation/nginx-pyapp-custom:build-$BUILD_NUMBER
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
