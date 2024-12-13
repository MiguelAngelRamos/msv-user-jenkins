pipeline {
    agent any
    environment {
        KUBERNETES_TOKEN = credentials('kubernetes-token') // Token del ServiceAccount
        KUBERNETES_CA_CERT = credentials('kubernetes-ca-cert-text') // Certificado CA
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
                script {
                    sh """
                        echo "Testing connection to Kubernetes..."
                        echo '${KUBERNETES_CA_CERT}' > /tmp/ca.crt
                        kubectl --server=${K8S_CLUSTER_URL} --token=${KUBERNETES_TOKEN} --certificate-authority=/tmp/ca.crt get namespaces
                        rm /tmp/ca.crt
                    """
                }
            }
        }
        stage('Apply Kubernetes Deployment') {
            steps {
                script {
                    sh """
                        echo "Applying deployment to Kubernetes..."
                        echo '${KUBERNETES_CA_CERT}' > /tmp/ca.crt
                        kubectl --server=${K8S_CLUSTER_URL} --token=${KUBERNETES_TOKEN} --certificate-authority=/tmp/ca.crt apply -f kubernetes/deployments/user-deployment.yaml -n production
                        rm /tmp/ca.crt
                    """
                }
            }
        }
    }
}
