pipeline {
    agent any
       environment {
           DOCKERHUB_CREDENTIALS = credentials('docker-hub-credentials')
           IMAGE_TAG = "dafik15/aws-inventory-project-app:${BUILD_NUMBER}"
           DOCKER_HUB_USERNAME = 'dafik15' 
           // SONAR_URL = "http://65.2.71.169:9000/"
        

       }
    stages {
        stage('Checkout') {
            steps {
                script {
                    git branch: 'main', url: 'https://github.com/Dafik-G-2/aws-inventory-project.git'
                }
            }
        }
        stage('Build and Test') {
            steps {
                bat 'mvn package'
            }
        }
        // stage('Static Code Analysis') {
        //     steps {
        //         withCredentials([string(credentialsId: 'sonar-creds', variable: 'TOKEN')]) {
        //             bat "mvn sonar:sonar -Dsonar.login=$TOKEN -Dsonar.host.url=${SONAR_URL}"
        //         }
        //     }
        // }
        // stage('Build Docker Image') {
        //     steps {
        //         script {
        //             bat "docker build -t ${IMAGE_TAG} ."
        //         }
        //     }
        // }
        // stage('Trivy Scan') {
        //     steps {
        //         script {
        //             // Assuming Trivy CLI is installed on Jenkins agent
        //             sh "trivy image --format json --output trivy-report.json ${IMAGE_TAG}"
                    
        //             // Optionally, you can parse the JSON report and fail the build on high-severity vulnerabilities
        //             sh """
        //             if grep -q '"Severity": "HIGH"' trivy-report.json; then
        //                 echo "High severity vulnerabilities found!"
        //                 exit 1
        //             fi
        //             """
        //         }
        //     }
        // }
        // stage('Push to Docker Hub') {
        //     steps {
        //         script {
        //             bat "echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login --username ${DOCKERHUB_CREDENTIALS_USR} --password-stdin"
        //             bat "docker push ${IMAGE_TAG}"
        //             bat "docker rmi ${IMAGE_TAG}"
        //         }
        //     }
        // }
        stage('Docker Login') {
            steps {
                withCredentials([string(credentialsId: 'docker-hub-token', variable: 'DOCKER_HUB_TOKEN')]) {
                    bat "docker image ls"
                    bat "docker build -t ${IMAGE_TAG} ."
                    bat 'echo $DOCKER_HUB_TOKEN | docker login -u $DOCKER_HUB_USERNAME --password-stdin'
                    // echo "dckr_pat_yIc3f9L7j3j2APOlHKXTq20NQvA" | docker login -u "dafik15" --password-stdin
                    bat "docker push ${IMAGE_TAG}"
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

