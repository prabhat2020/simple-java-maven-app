pipeline {
       agent any
  tools {
    maven 'Maven'
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
         
         
         
        
         
         
         
         
        
  
      }
}
