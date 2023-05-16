pipeline {
    agent { label 'jenkins-slave' }
    stages {
        stage('build') {
            steps {
                script {
                   withCredentials([usernamePassword(credentialsId: 'dockerhubaccount', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) 
                    {
                       sh """
                            docker login -u $USERNAME -p $PASSWORD
                            docker build -t monasamir/server:v${BUILD_NUMBER} -f $WORKSPACE/badreads-backend/Dockerfile  
                            docker push monasamir/server:v${BUILD_NUMBER} 
                       """
                   
                       sh """
                            docker login -u $USERNAME -p $PASSWORD
                            docker build -t monasamir/client:v${BUILD_NUMBER} -f $WORKSPACE/badreads-frontend/Dockerfile  
                            docker push monasamir/client:v${BUILD_NUMBER}
                       """
                   }
                }
            }
        }
             parameters {
         choice(name: 'build', choices: [dev,test,prod], description: 'choose a value')
       }
        
            stage('build') {
           steps {
            withCredentials([file(credentialsId: 'kubeconfig-credi', variable: 'KUBECONFIG')])
            { 
                if(build.equals('dev')){
            sh """
                echo "Running Helm"
                helm install dev${BUILD_NUMBER} ./HELM/onboard-task --values dev.yaml
               """
            }
               if(build.equals('test'))
             {
            sh """
                echo "Running Helm"
                helm install test${BUILD_NUMBER} ./HELM/onboard-task --values test.yaml
               """

             }
             else(build.equals('test'))
             {
            sh """
                echo "Running Helm"
                helm install prod${BUILD_NUMBER} ./HELM/onboard-task --values prod.yaml
               """

             }

            }
        
      }
        
    }
}
