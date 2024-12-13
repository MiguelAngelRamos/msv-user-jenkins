pipeline {
    agent any
    environment {
        KUBERNETES_TOKEN = credentials('kubernetes-token') // Token del ServiceAccount
        K8S_CLUSTER_URL = 'https://192.168.49.2:8443' // URL del clúster
    }
    stages {
        stage('Test Kubernetes Connection') {
            steps {
                script {
                    sh """
                        echo "Testing connection to Kubernetes..."
                        kubectl --server=${K8S_CLUSTER_URL} --token=${KUBERNETES_TOKEN} --certificate-authority=/root/ca.crt get namespaces
                    """
                }
            }
        }
    }
}
