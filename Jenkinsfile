pipeline {
       agent any
  tools {
    maven 'Maven'
  }
       environment {
        // This can be nexus3 or nexus2
        NEXUS_VERSION = "nexus3"
        // This can be http or https
        NEXUS_PROTOCOL = "http"
        // Where your Nexus is running
        NEXUS_URL = "52.143.7.186/nexuss-1336430"
        // Repository where we will upload the artifact
        NEXUS_REPOSITORY = "repository-example"
        // Jenkins credential id to authenticate to Nexus OSS
        NEXUS_CREDENTIAL_ID = "c0f748c6-fbf2-41a0-8672-4fa953220455"
        registry = "prabhat2020/testing"
        registryCredential = "Test1234"
    }
  stages {
       stage('Initialize')
         {
                steps
                {
                       script
                       {
         def dockerHome = tool 'MyDocker'
         def mavenHome  = tool 'Maven'
        env.PATH = "${dockerHome}/bin:${mavenHome}/bin:${env.PATH}"
        sh "which docker"
        sh "/usr/bin/docker -v"
         }
                }
         }
       stage('Build'){
           steps
           {
        sh "mvn clean install"
    }     
    }
         stage('Sonar'){
                steps
                {
        
            sh "mvn sonar:sonar -Dsonar.host.url=http://52.143.7.186/prabhat-sonarqube -Dsonar.login=32672e636f2542b85824fc63195492c909b2f4a0"
        
                }
     }
         stage('Building our image') { 
             steps { 
                    script {
                 dockerImage = docker.build registry + ":$BUILD_NUMBER"
                    }
                 } 

     }
      stage('Deploy our image') { 
          steps { 
                 script { 
                  docker.withRegistry( '', registryCredential ) { 
                  dockerImage.push() 
                        }
                   } 
          }
      }
      }
}
