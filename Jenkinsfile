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
    }
}