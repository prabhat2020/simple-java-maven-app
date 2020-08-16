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
        NEXUS_URL = "52.143.7.186/prabhat-nexus"
        // Repository where we will upload the artifact
        NEXUS_REPOSITORY = "repository-example"
        // Jenkins credential id to authenticate to Nexus OSS
        NEXUS_CREDENTIAL_ID = "2ad39ac7-1592-43f4-9235-a12d0fddc9f4"
        
    }
  stages {
    stage('Initialize'){
      steps{
        echo "We are doing some test"
        echo "PATH = ${PATH}"
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
        
            sh "mvn sonar:sonar -Dsonar.host.url=http://52.143.7.186/prabhat-sonarqube -Dsonar.login=5efb35ef382e6d046238e328a216434092dcc429"
        
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
