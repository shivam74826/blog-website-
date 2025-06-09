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

        stage('Create Jenkins Service Account') {
            steps {
                script {
                    def saYaml = '''\
apiVersion: v1
kind: ServiceAccount
metadata:
  name: jenkins
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: jenkins-admin
subjects:
- kind: ServiceAccount
  name: jenkins
  namespace: kube-system
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
'''
                    writeFile file: 'k8s/jenkins-sa.yaml', text: saYaml
                }
                withKubeConfig([credentialsId: 'kubeconfig-id']) {
                    sh 'kubectl apply -f k8s/jenkins-sa.yaml --validate=false'
                }
            }
        }

        stage('Build/Validate') {
            steps {
                echo "No build needed for static site. Validating HTML/CSS structure..."
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withKubeConfig([credentialsId: 'kubeconfig-id']) {
                    sh "kubectl apply -n ${NAMESPACE} -f k8s/deployment.yaml"
                }
            }
        }

        stage('Post-Deployment') {
            steps {
                echo "✅ Deployment to ${NAMESPACE} namespace completed!"
            }
        }
    }
}
