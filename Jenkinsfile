pipeline {
    agent any
    
    tools {
        maven 'maven3'
        jdk 'jdk17'
    }
    environment {
    registry = "nokha/hello"
    registryCredential = '3ef5111e-d9a1-46d4-8816-fbded5756485'
    dockerImage = ''
}

    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/codetobuild/simple-springapp']])
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        
        stage('Code Quality Analysis'){
                
            steps {
                script {
                    withSonarQubeEnv('sonarserver') {
                        sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=springapp -Dsonar.projectName=springapp -Dsonar.host.url=http://localhost:9000'
                    }
                }
        }
        }
        stage('Build docker image'){
            steps{
                script{
                    // sh 'docker build -t springappdemo .'
                  dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
      stage('Publish Docker Image') {
          steps{
            script {
              docker.withRegistry( '', registryCredential ) {
                dockerImage.push()
              }
            }
          }
        }
        stage('Deploy Locally') {
            steps {
                // Pull the Docker image from Docker Hub and run it locally
                sh "docker pull ${registry}:${BUILD_ID}"
                sh "docker run -d -p 8083:8083 ${registry}:${BUILD_ID}"
            }
        }
    }
    post{
        success{
            echo "your docker image is running...."
            echo "follow url to access application http://localhost:8083/"
            
        }
    }
}
