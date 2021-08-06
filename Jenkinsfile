pipeline {
    agent any
   
    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "maven3"
    }
    environment {
      DOCKER_TAG = getVersion()
    }
    
    stages {
        stage('Build') {
            steps {
                // Get some code from a GitHub repository
                git 'https://github.com/sakthinatural/dockeransiblejenkins.git'

                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }
        stage('Build Docker image') {
            steps {
                sh "docker build . -t sakthinatural123/hariapp:${DOCKER_TAG}"
            }
        }
        stage('Docker push'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerhubpwd')]) {
                    sh "docker login -u sakthinatural123 -p ${dockerhubpwd}"
                }
                sh "docker push sakthinatural123/hariapp:${DOCKER_TAG}"
            }
        }
        
        stage('Docker Run on prod server'){
            steps{
                ansiblePlaybook credentialsId: 'dev-server', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
        
        
        
        
    }
}

def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}



