pipeline {
  agent any 

  stages {
      stage('checkout'){
          steps {
            checkout scm

          }
      }
    stage('build'){
          steps {

            sh 'npm install'
          }
      }
    stage('test'){
          steps {
                
            sh 'npm test'
          }
      }
    stage('Build Docker Image'){
          steps {
                echo 'build docker image'
            script {
                    def branchName = env.BRANCH_NAME
                    def imageName = branchName == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'
                    
                    
                    sh "docker build -t ${imageName} ."
                    
                  
                    sh """
                    CONTAINER_ID=\$(docker ps -q -f name=${imageName})
                    if [ -n "\$CONTAINER_ID" ]; then
                        echo "Stopping and removing existing container..."
                        docker stop \$CONTAINER_ID
                        docker rm \$CONTAINER_ID
                    fi
                    """
                }
          }
      }
    stage('deploy'){
          steps {
                echo 'deploy app'
            script {
                    def branchName = env.BRANCH_NAME
                    def imageName = branchName == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'
                    def portMapping = branchName == 'main' ? '3000:3000' : '3001:3000'
                    


              
                    sh "docker run -d --expose ${portMapping.split(':')[0]} -p ${portMapping} --name ${imageName} ${imageName}"
                }
          }
      }

  }


  
}
