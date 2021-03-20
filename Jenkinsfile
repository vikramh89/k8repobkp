pipeline {
    agent any
    environment{
        DOCKER_TAG = getDockerTag()
          }
    stages{
        stage('Build Docker Image'){
            steps{
                sh "docker build . -t kammana/nodeapp:${DOCKER_TAG}"
            }
        }
        stage('DockerHub Push'){
            steps{
                withCredentials([string(credentialsId: 'docker-hunb', variable: 'dockerHubPwd')]) {
                    sh "docker login -u kammana -p ${dockerHubPwd}"
                    sh "docker push kammana/nodeapp:${DOCKER_TAG}"
                }
            }
        }
        stage('Deploy to k8s'){
            steps{
		    sh "chmod +x changeTag.sh"
		    sh "./changeTag.sh ${DOCKER_TAG}"
                    sshagent(['kubernetes']) {
                         "scp -o StrictHostKeyCheckin=no services.yml node-app-pod.yml ubuntu@34.216.186.50:/home/ubuntu"    
                    script {
                          try {
          			sh "ssh ubuntu@34.216.186.50 kubectl apply -f ."
         			} catch(error){
          				sh "ssh ubuntu@34.216.186.50 kubectl create -f ."
         		        }
        		    }
                         }
                }
            }
        }
    }
}
