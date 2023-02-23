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
               script
               {
                if ("${GIT_BRANCH}" == 'origin/main'){
                    sh '''
                    sed -e 's,{{namespace}},production,g;' application.yml | kubectl apply -f -    
                    cd ./nginx
                    sed -e 's,{{namespace}},production,g;' nginx.yml | kubectl apply -f -
                    kubectl rollout restart deployment lbg-python-app
                    kubectl rollout restart deployment nginx-python-app
                    '''
               }
               else if("${GIT_BRANCH}" == 'origin/ps-python-apibranch')
               {
                    sh '''
                    sed -e 's,{{namespace}},development,g;' application.yml | kubectl apply -f -
                    cd ./nginx
                    sed -e 's,{{namespace}},development,g;' nginx.yml | kubectl apply -f -
                    kubectl rollout restart deployment lbg-python-app
                    kubectl rollout restart deployment nginx-python-app          
                    '''
               } 

            }          
        }
      }
   }
}
