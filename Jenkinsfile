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
        registryCredential = "06216ef3-ad77-49a6-a37b-e2e91cd08bfb"
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
         stage("publish to nexus") {
            steps {
                script {
                    // Read POM xml file using 'readMavenPom' step , this step 'readMavenPom' is included in: https://plugins.jenkins.io/pipeline-utility-steps
                    pom = readMavenPom file: "pom.xml";
                    // Find built artifact under target folder
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    // Print some info from the artifact found
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    // Extract the path from the File found
                    artifactPath = filesByGlob[0].path;
                    // Assign to a boolean response verifying If the artifact name exists
                    artifactExists = fileExists artifactPath;
                    if(artifactExists) {
                        echo "* File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
                            version: pom.version,
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                // Artifact generated such as .jar, .ear and .war files.
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging],
                                // Lets upload the pom.xml file for additional information for Transitive dependencies
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"]
                            ]
                        );
                    } else {
                        error "* File: ${artifactPath}, could not be found";
                    }
                }
            }
        }
         
      }
}
