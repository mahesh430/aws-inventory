pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-credentials')
        IMAGE_TAG = "mahesh430/avw-inventory:${BUILD_NUMBER}"
        SONAR_URL = "http://52.91.246.234:9000/"
        

    }
    stages {
        stage('Checkout') {
            steps {
                script {
                    git branch: 'main', url: 'https://github.com/mahesh430/aws-inventory.git'
                }
            }
        }
        stage('Build and Test') {
            steps {
                sh 'ls -ltr'
                sh 'mvn clean package'
            }
        }
        stage('Static Code Analysis') {
            steps {
                withCredentials([string(credentialsId: 'sonarqube', variable: 'SONAR_AUTH_TOKEN')]) {
                    sh "mvn sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}"
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    
                    sh "docker build -t ${IMAGE_TAG} ."
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                script {
                    sh "echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login --username ${DOCKERHUB_CREDENTIALS_USR} --password-stdin"
                    sh "docker push ${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_TAG}"
                }
            }
        }
    
    }
   // post {
       // always {
       //      // Clean up the workspace
       //      deleteDir()

       //      // Docker cleanup commands (consider adding if appropriate)
       //      script {
       //          sh "docker container prune -f"
       //          sh "docker image prune -f"
       //          sh "docker volume prune -f"
       //      }
       //  }
  //  }
}
   stage('Docker Login') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-creds', variable: 'dockerpwd')]) {
                    sh 'echo "${dockerpwd}" | docker login -u my-docker-username --password-stdin'
                }
            }
        }
stage('Docker Push') {
            steps {
                sh 'docker push my-image:latest'
            }
        }
