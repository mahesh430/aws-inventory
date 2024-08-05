awspipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-credentials')
        IMAGE_TAG = "mahesh430/aws-inventory-app:${BUILD_NUMBER}"
        SONAR_URL = "http://54.226.161.29:9000/"
        GIT_USER_NAME = "mahesh430"
        GIT_REPO_NAME = "https://github.com/mahesh430/aws-inventory-k8s.git"
        
        

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
        stage('Trivy Scan') {
            steps {
                script {
                    // Assuming Trivy CLI is installed on Jenkins agent
                    sh "trivy image --format json --output trivy-report.json ${IMAGE_TAG}"
                    
                    // Optionally, you can parse the JSON report and fail the build on high-severity vulnerabilities
                    sh """
                    if grep -q '"Severity": "HIGH"' trivy-report.json; then
                        echo "High severity vulnerabilities found!"
                        exit 1
                    fi
                    """
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
        stage('Update Deployment File') {
    steps {
        withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
            script {
                try {
                    sh '''
                        set -e
                        cd ~
                        pwd
                        git clone https://github.com/${GIT_USER_NAME}/${GIT_REPO_NAME}.git
                        cd ${GIT_REPO_NAME}
                        # Update the deployment file
                        sed -i "s|image: mahesh430/aws-inventory-app:*|image: mahesh430/aws-inventory-app:${BUILD_NUMBER}|g" k8s/deployment.yml
                        git config user.email "umamahesh690@gmail.com"
                        git config user.name "Mahesh"
                        git add k8s/deployment.yml
                        git commit -m "Update deployment file with image version ${BUILD_NUMBER}"
                        git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                    '''
                } catch (Exception e) {
                    echo "Error occurred during deployment file update: ${e.getMessage()}"
                    throw e
                }
            }
        }
    }
}
b
    
    }
  
}

