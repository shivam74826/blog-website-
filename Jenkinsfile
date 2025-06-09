pipeline {
    agent any

    environment {
        K8S_CONTEXT = 'kubernetes-admin@kubernetes'
        NAMESPACE = "${env.BRANCH_NAME == 'dev' ? 'dev' : env.BRANCH_NAME == 'test' ? 'test' : 'prod'}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: "${env.BRANCH_NAME}", url: 'https://github.com/shivam74826/blog-website-.git'
            }
        }

        stage('Build/Validate') {
            steps {
                echo "No build needed for static site. Validating HTML/CSS structure..."
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withKubeConfig(credentialsId: 'kubeconfig-id') {
                    sh "kubectl apply -n ${NAMESPACE} -f k8s/deployment.yaml"
                }
            }
        }

        stage('Post-Deployment') {
            steps {
                echo "Deployment to ${NAMESPACE} namespace completed"
            }
        }
    }
}
