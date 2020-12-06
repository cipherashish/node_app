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
               if [ $(docker ps -qf "name=nodejs_app") ]
                then
                echo "from if block"
                docker kill nodejs_app && docker rm nodejs_app
                docker run -d -p 9876:8080 --name nodejs_app "${registry}":"${BUILD_NUMBER}"
                docker ps
               else
                echo "from else block"
                docker run -d -p 9876:8080 --name nodejs_app "${registry}":"${BUILD_NUMBER}"
                docker ps
                fi
               '''
           }
       }   
    
     }
}
