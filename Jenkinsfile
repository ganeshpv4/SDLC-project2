pipeline{

    agent any
    
    environment{
        NUMBER = "${env.BUILD_ID}"
        recipientEmails = "pvganesh4@gmail.com"
    }
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
//        stage("Push artifacts to nexus"){
//            steps{
//                script{
//
//                    def PomVersion = readMavenPom file: 'pom.xml'
//                    def NexusRepo = PomVersion.version.endsWith("SNAPSHOT") ? "demoapp-snapshot" : "demoapp-release" 
//
//                    nexusArtifactUploader artifacts: [[artifactId: 'CubeGeneratorWeb', 
//                    classifier: '', 
//                    file: "target/SDLC-both.war", 
//                    type: 'war']], 
                    
//                    credentialsId: 'nexus', 
//                    groupId: 'com.javatpoint',
//                    nexusUrl: '34.201.5.4:8081', 
//                    nexusVersion: 'nexus3', 
//                    protocol: 'http', 
//                    repository: "${NexusRepo}", 
//                    version: "${PomVersion.version}"
//                }
//            }
//        }

        stage("Push to S3 bucket"){
            steps{
                script{

                    s3Upload consoleLogLevel: 'INFO', 
                    dontSetBuildResultOnFailure: false, 
                    dontWaitForConcurrentBuildCompletion: false, 
                    entries: [[bucket: 'artifact-from-jenkinsfile', 
                    excludedFile: '', 
                    flatten: false, 
                    gzipFiles: false, 
                    keepForever: false, 
                    managedArtifacts: false, 
                    noUploadOnFailure: true, 
                    selectedRegion: 'us-east-1', 
                    showDirectlyInBrowser: false, 
                    sourceFile: 'target/SDLC-both.war', 
                    storageClass: 'STANDARD', 
                    uploadFromSlave: false, 
                    useServerSideEncryption: false]], 
                    pluginFailureResultConstraint: 'FAILURE', 
                    profileName: 'AWS_S3', 
                    userMetadata: []
                
                }
            }
        }

    }
     post{
        always{
            emailext to: "${recipientEmails}",
            subject: "Jenkins build:${currentBuild.currentResult}: ${env.JOB_NAME}",
            body: "${currentBuild.currentResult}: Job ${env.JOB_NAME}\nMore Info can be found here: ${env.BUILD_URL}"
        }
    }
}