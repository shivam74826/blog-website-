pipeline {
    agent any
    environment {
        NAMESPACE = "${env.BRANCH_NAME == 'dev' ? 'dev' : env.BRANCH_NAME == 'test' ? 'test' : 'prod'}"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: "${env.BRANCH_NAME}", url: 'https://github.com/<your-repo>.git'
            }
        }
        stage('Build') {
            steps {
                echo "Building for ${env.BRANCH_NAME} environment"
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh "kubectl apply -n ${NAMESPACE} -f k8s/deployment.yaml"
                }
            }
        }
    }
}

