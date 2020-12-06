@Library('lib')_

pipeline {
    environment {
        registry = "marvelmeluha/app_node"
        registryCredential = 'docker'
        dockerImage = ''
    }
    agent any
    stages{
        stage('Checkout'){
         steps{ 
            github_checkout("main", "https://github.com/cipherashish/node_app.git")
         }
        }
            
        stage('Initialize'){
            steps {
                script {
                    def dockerHome = tool 'aye_Docker'
                    env.PATH = "${dockerHome}/bin:${env.PATH}"
                }
            }
        }
         
        stage('Building our image')
        {
            steps
            {
                sh 'docker --version'
                script
                {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Push image') {
            steps {
                script {
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                    }
                }
            }
        }
        
        stage('Pull image'){

             steps{

                 script{

                     docker.withRegistry( '', registryCredential ) {

                         dockerImage.pull()


                 }

             }

             sh  'docker images'

         }

     }
      stage('Run Image'){
           steps{
               sh ''' 
               if [ $(docker ps -qf "name=app_node") ]
                then
                echo "from if block"
                docker kill app_node && docker rm app_node
                docker run -d -p 8765:8080 --name nodejs_app "${registry}":"${BUILD_NUMBER}"
                docker ps
               else
                echo "from else block"
                docker run -d -p 8765:8080 --name nodejs_app "${registry}":"${BUILD_NUMBER}"
                docker ps
                fi
               '''
           }
       }   
    
     }
}
