// pipeline {
//   agent any 

//   environment {
//           PATH = "/usr/local/bin:${env.PATH}"  // Adjust the path as necessary
//       }
    
//   stages {

    
//       stage('checkout'){
//           steps {
//             checkout scm

//           }
//       }
//     stage('build'){
//           steps {

//             sh 'npm install'
//           }
//       }
//     stage('test'){
//           steps {
                
//             sh 'npm test'
//           }
//       }
//     stage('Build Docker Image'){
//           steps {
//                 echo 'build docker image'
//             script {
//                     def branchName = env.BRANCH_NAME
//                     def imageName = branchName == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'
                    
                    
//                     sh "docker build -t ${imageName} ."
                    
                  
//                     sh """
//                     CONTAINER_ID=\$(docker ps -q -f name=${imageName})
//                     if [ -n "\$CONTAINER_ID" ]; then
//                         echo "Stopping and removing existing container..."
//                         docker stop \$CONTAINER_ID
//                         docker rm \$CONTAINER_ID
//                     fi
//                     """
//                 }
//           }
//       }
//     stage('deploy'){
//           steps {
//                 echo 'deploy app'
//             script {
//                     def branchName = env.BRANCH_NAME
//                     def imageName = branchName == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'
//                     def portMapping = branchName == 'main' ? '3000:3000' : '3001:3000'
                    


              
//                     sh "docker run -d --expose ${portMapping.split(':')[0]} -p ${portMapping} --name ${imageName} ${imageName}"
//                 }
//           }
//       }

//   }

@Library('shared-lib') _
pipeline {
    agent any
    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub'
        DOCKER_REGISTRY = 'gevorgyan'
        IMAGE_NAME = 'nodemain'
        IMAGE_TAG = 'v1.0'
        CONTAINER_NAME = 'nodemain'
        PORT = '3000'
    }
    stages {
        stage('Setup Docker') {
            steps {
                dockerSetup()
            }
        }
        stage('Pull Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: env.DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        docker login -u "${DOCKER_USER}" --password "${DOCKER_PASS}"
                        docker pull ${DOCKER_REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }
        stage('Deploy Docker Image') {
            steps {
                script {
                    sh '''
                        docker stop $(docker ps -q --filter "name=${CONTAINER_NAME}") || true
                        docker rm $(docker ps -a -q --filter "name=${CONTAINER_NAME}") || true
                    '''
                    sh "docker run -d --name ${CONTAINER_NAME} --expose ${PORT} -p ${PORT}:3000 ${DOCKER_REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }
    }
}

  
// }
