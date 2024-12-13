pipeline {
    agent any
    tools {
        maven 'maven_jenkins' // Nombre configurado para Maven en Jenkins
    }
    parameters {
        string(name: 'SERVICE_NAME', defaultValue: 'user-service-2', description: 'Nombre del microservicio (user-service-2 o order-service-2)')
        string(name: 'DEPLOYMENT_FILE', defaultValue: 'user-deployment.yaml', description: 'Archivo YAML de despliegue')
    }
    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials')
        KUBERNETES_TOKEN = credentials('kubernetes-token')
        KUBERNETES_CA_CERT = credentials('kubernetes-ca-cert-text')
        K8S_CLUSTER_URL = 'https://192.168.49.2:8443'
    }
    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }
        stage('Build JAR') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh """
                        docker buildx build --platform linux/amd64 -t mramoscli/${SERVICE_NAME}:latest .
                        docker login -u ${DOCKER_HUB_CREDENTIALS_USR} -p ${DOCKER_HUB_CREDENTIALS_PSW}
                        docker push mramoscli/${SERVICE_NAME}:latest
                    """
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh """
                        echo "${KUBERNETES_CA_CERT}" > /tmp/ca.crt
                        kubectl --server=${K8S_CLUSTER_URL} --token=${KUBERNETES_TOKEN} --certificate-authority=/tmp/ca.crt apply -f kubernetes/deployments/${DEPLOYMENT_FILE} -n production
                        rm /tmp/ca.crt
                    """
                }
            }
        }
    }
}
