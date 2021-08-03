pipeline {
    
    agent any
    
    environment {
        dockerImage =''
        registry = 'abhilashnarayan/pythonapp'
        registryCredential = 'dockerhub-id'
    }
    
    stages {
        stage ('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://bitbucket.org/ananthkannan/mypythonrepo/']]])
            }
        }
        
        stage ('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build registry
                }
            }
        }
        
        stage ('Uploading Image') {
            steps {
                script {
                    docker.withRegistry( '', registryCredential ) {
                    dockerImage.push()
                    }
                }
            }
        }
        
         // Stopping Docker containers for cleaner Docker run
        stage('docker stop container') {
            steps {
              sh 'docker ps -f name=mypythonappContainer -q | xargs --no-run-if-empty docker container stop'
              sh 'docker container ls -a -fname=mypythonappContainer -q | xargs -r docker container rm'
            }
       } 
       
       // Running Docker container, make sure port 8096 is opened in 
        stage('Docker Run') {
            steps{
                script {
                    dockerImage.run("-p 8096:5000 --rm --name mypythonappContainer")
                }
            }
        }
        
    }
}
