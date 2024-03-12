pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven3'
    }
    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout From Git') {
            steps {
                git branch: 'main', url: 'https://github.com/babaly/javaApp-CICD.git'
            }
        }
        stage('mvn compile') {
            steps {
                sh 'mvn clean compile'
            }
        }
        stage('mvn test') {
            steps {
                sh 'mvn test'
            }
        }
        
        stage('Sonarqube Analysis ') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Pet-Clinic \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=Petclinic '''
                }
            }
        }
        stage('quality gate') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
        stage('mvn build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('OWASP Dependency Check') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --format HTML ', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.html'
            }
        }
        stage('Docker Build & Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh 'docker build -t spring-app .'
                    }
                }
            }
        }
        stage('Clean up containers') {   //if container runs it will stop and remove this block
            steps {
                script {
                    try {
                        sh 'docker stop spring-container'
                        sh 'docker rm spring-container'
                } catch (Exception e) {
                        echo 'Container spring-container not found, moving to next stage'
                    }
                }
            }
        }
        stage('Deploy to conatainer') {
            steps {
                sh 'docker run -d --name spring-container -p 8082:8080 spring-app'
            }
        }
    }
}
