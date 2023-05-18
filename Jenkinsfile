pipeline {
    agent { label 'jenkins-slave' }
      parameters {
         choice(name: 'build', choices: [dev,test,prod], description: 'choose a value')
       }
    stages {
        stage('build') {
            steps {
                script {
                   withCredentials([usernamePassword(credentialsId: 'dockerhubaccount', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) 
                    {
                       sh """
                            docker login -u $USERNAME -p $PASSWORD
                            docker build -t monasamir/server:b${BUILD_NUMBER} -f $WORKSPACE/badreads-backend/Dockerfile  
                            docker push monasamir/server:b${BUILD_NUMBER} 
                          """
                   
                       sh """
                            docker login -u $USERNAME -p $PASSWORD
                            docker build -t monasamir/client:v${BUILD_NUMBER} -f $WORKSPACE/badreads-frontend/Dockerfile  
                            docker push monasamir/client:b${BUILD_NUMBER}
                          """
                   }
                }
            }
        }
        stage('deploy') {
                steps {          
                  script {
                    withCredentials([file(credentialsId: 'kubeconfig-credi', variable: 'KUBECONFIG')])
                      { 
                             if (deploy.equals("dev"))
                                {
                                  sh """
                                    echo "Running Helm"
                                   helm install dev${BUILD_NUMBER} ./HELM/onboard-task --values dev.yaml
                                    """
                               }
                             else if (deploy.equals("test"))
                              {
                                 sh """
                                   echo "Running Helm"
                                   helm install test${BUILD_NUMBER} ./HELM/onboard-task --values test.yaml
                                   """
                               }
                             else if (deploy.equals("prod"))
                              {
                               sh """
                                 echo "Running Helm"
                                 helm install prod${BUILD_NUMBER} ./HELM/onboard-task --values prod.yaml
                                  """
                              }
                               
                             else
                              {
                               sh """
                                 echo "done"
                                  """
                              }
                          end 
                     }
                }
            }
        }
    }  
}
