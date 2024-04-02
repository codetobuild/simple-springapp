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
        stage('checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/codetobuild/simple-springapp.git'
                echo 'Hello World'checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/codetobuild/simple-springapp']])
            }
        }
        stage('build maven') { // Added a stage name
            steps {
                sh 'mvn clean install'
            }
        }
        stage('buld docker image'){
            steps{
                script{
                    // sh 'docker build -t springappdemo .'
                  dockerImage = docker.build registry + ":$BUILD_NUMBER"

                }
            }
        }
      stage('Deploy Image') {
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
