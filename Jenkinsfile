pipeline {
    agent any
    tools {
        maven 'M2_HOME'
    }
    environment {
        DOCKER_TAG = getVersion()
    }
    stages {
        stage('SCM ') {
            steps {
                git credentialsId: 'github', 
                    url: 'https://github.com/Valiveti-Neelima/dockeransiblejenkins.git'
            }
        }
        stage('Maven Build'){
            steps {
                sh "mvn clean package"
            }
        }
        stage('Docker Build'){
            steps{
                sh "docker build . -t valiveti/hariapp:${DOCKER_TAG} "
            }
        }
        stage('DockerHub push'){
            steps{
                withCredentials([string(credentialsId: 'Dockerhub', variable: 'dockerHubPwd')]) {
                    sh "docker login -u valiveti -p ${dockerHubPwd}"
                    
                }
                sh "docker push valiveti/hariapp:${DOCKER_TAG} "
            }
        }
        stage('Docker depoly'){
            steps {
                ansiblePlaybook credentialsId: 'dev-server', disableHostKeyChecking: true, 
                extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', 
                playbook: 'deploy-docker.yml'
                
            }
            
        }
    }
    
}
def getVersion(){
    def commitHash=sh returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
