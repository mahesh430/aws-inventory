pipeline {
    agent any
       environment {
           DOCKERHUB_CREDENTIALS = credentials('docker-hub-credentials')
           IMAGE_TAG = "Dafik-G-2/aws-inventory-project-app:${BUILD_NUMBER}"
           SONAR_URL = "http://34.204.40.173:9000/"
        

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
                sh 'mvn package'
            }
        }
        stage('Static Code Analysis') {
            steps {
                withCredentials([string(credentialsId: 'sonar-creds', variable: 'TOKEN')]) {
                    sh "mvn sonar:sonar -Dsonar.login=$TOKEN -Dsonar.host.url=${SONAR_URL}"
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

