pipeline{
    agent any
    /* tools{
        jdk 'jdk17'
        maven 'maven3'
    }
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    } */
    stages {
        stage('clean workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Checkout From Git'){
            steps{
                git branch: 'main', url: 'https://github.com/babaly/javaApp-CICD.git'
            }
        }
        stage('mvn compile'){
            steps{
                sh 'mvn clean compile'
            }
        }
        stage('mvn test'){
            steps{
                sh 'mvn test'
            }
        }
        
                
        } 
    }
}
   