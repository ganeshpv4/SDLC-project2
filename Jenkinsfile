pipeline{

    agent any
    
    tools { 
      maven 'Maven-3.8.7' 
      jdk 'Java-11' 
    }

    stages{

        stage("Git checkout"){

            steps{
               git branch: 'main',  
               url: 'https://github.com/ganeshpv7/SDLC-project2.git'
            }
        }
        stage("Unit test"){

            steps{
                sh 'mvn test'
            }
        }
        stage("Integration test"){
            steps{
                sh 'mvn verify -DskipUnitTest'
         }
       }
        stage("Maven build"){
            steps{
                sh 'mvn clean install'
            }
        }
        stage("Sonarqube analysis"){
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar-key'){
                        sh 'mvn clean package sonar:sonar'
                   }
                }
            }
        }
        stage("Quality gate analysis"){
            steps{
                script{
                    waitForQualityGate abortPipeline: false, 
                    credentialsId: 'sonar-key'
                }
            }
        }
        stage("Push artifacts to nexus"){
            steps{
                script{

                    def PomVersion = readMavenPom file: 'pom.xml'
                    

                    nexusArtifactUploader artifacts: [[artifactId: 'CubeGeneratorWeb', 
                    classifier: '', 
                    file: 'target/CubeGeneratorWeb.war', 
                    type: 'war']], 
                    credentialsId: 'nexus', 
                    groupId: 'com.javatpoint',
                    nexusUrl: '34.201.47.58:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: 'demoapp-snapshot', 
                    version: "${PomVersion.version}"
                }
            }
        }
    }
}