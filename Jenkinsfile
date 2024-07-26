pipeline {
    agent any
    environment {
        IMAGE_TAG = 'mahesh430/inventory-app'
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-credentials')
    }
    stages {
        stage("Git checkout"){
            steps {
               git branch: 'main', url: 'https://github.com/mahesh430/aws-inventory.git'
            }
        }
        //  stage("Build artificat"){
        //     steps {
        //       sh 'mvn package'
        //     }
        // }
         stage("Docker image creation"){
            steps {
             sh 'docker build -t ${IMAGE_TAG} .'
            }
        }
        stage("Docker Login and push") {
            steps{
                script {
                    sh "echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login --username ${DOCKERHUB_CREDENTIALS_USR} --password-stdin"
                    sh "docker push ${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_TAG}"
                }
        }
        }
           
    }
    
}
