pipeline {
    agent any
    tools {
        maven 'Maven3.6.3'
        
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/Sathish-1985/Docker-Demo']]])
            }
        }
    
           stage('Build') {
            steps {
               
                sh 'mvn clean install -f pom.xml'
                 slackSend channel: '#alert', message: 'Build process completed' 
        }

    }
    
    stage('Docker Build and Tag') {
           steps {
              
                sh 'docker build -t samplewebapp:latest .' 
                sh 'docker tag samplewebapp sathishsekhar/samplewebapp:latest'
                sh 'docker tag samplewebapp sathishsekhar/samplewebapp:$BUILD_NUMBER'
               
          }
        }
          stage('Publish image to Docker Hub') {
          
            steps {
        withDockerRegistry([ credentialsId: "docker", url: "" ]) {
          sh  'docker push sathishsekhar/samplewebapp:latest'
           sh  'docker push sathishsekhar/samplewebapp:$BUILD_NUMBER' 
        }
                  
          }
        }
        
              stage('Run Docker container on Jenkins Agent') {
             
            steps 
   {
                sh "docker run -d -p 8087:8080 sathishsekhar/samplewebapp"
 
            }
        }
        
        stage('Run Docker container on Prodution server') {
             
          steps {
             sh "docker -H ssh://jenkins@35.239.81.216 run -d -p 8085:8080 sathishsekhar/samplewebapp"
 
            }
       }
}

}
