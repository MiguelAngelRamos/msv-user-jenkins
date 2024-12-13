pipeline {
    agent any
    environment {
        KUBERNETES_TOKEN = credentials('kubernetes-token') // Token del ServiceAccount
        K8S_CLUSTER_URL = 'https://192.168.49.2:8443' // URL del clúster
    }
    stages {
        stage('Checkout Deployment Files') {
            steps {
                checkout scm
            }
        }
        stage('Test Kubernetes Connection') {
            steps {
                withCredentials([file(credentialsId: 'kubernetes-ca-cert', variable: 'CA_CERT')]) {
                    sh """
                        echo "Testing connection to Kubernetes..."
                        kubectl --server=${K8S_CLUSTER_URL} --token=${KUBERNETES_TOKEN} --certificate-authority=$CA_CERT get namespaces
                    """
                }
            }
        }
        stage('Apply Kubernetes Deployment') {
            steps {
                withCredentials([file(credentialsId: 'kubernetes-ca-cert', variable: 'CA_CERT')]) {
                    sh """
                        echo "Applying deployment to Kubernetes..."
                        kubectl --server=${K8S_CLUSTER_URL} --token=${KUBERNETES_TOKEN} --certificate-authority=$CA_CERT apply -f kubernetes/deployments/user-deployment.yaml -n production
                    """
                }
            }
        }
    }
}
