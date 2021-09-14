pipeline{
    agent any
    tools {
      maven 'maven3'
    }
    environment {
      DOCKER_TAG = getVersion()
    }

    stages {
        
        stage ("SCM") {
            steps {
                git credentialsId: 'github', 
                url: 'https://github.com/amashhour1974/dockeransiblejenkins'
            }
        }
        
        stage('Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }
        
        stage('Docker Build'){
            steps{
            
                sh 'docker build . -t amashhour/hariapp:${DOCKER_TAG} '
             
            }
        }
        
         stage('Docker Push'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
                 sh 'docker login -u amashhour -p ${dockerHubPwd}'
                }
                
                sh 'docker push amashhour/hariapp:${DOCKER_TAG} '
            }
        }
        
        stage('Docker Deploy'){
            steps{
           ansiblePlaybook credentialsId: 'ansible-dev3', disableHostKeyChecking: true, extras: '-e DOCKER_TAG=${DOCKER_TAG}', installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'        
                
            }
        }
      
    }
}

def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
