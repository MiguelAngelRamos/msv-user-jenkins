pipeline {
    agent any
    tools {
        maven 'maven_jenkins' // Nombre configurado para Maven en Jenkins
    }
    environment {
        SERVICE_NAME = 'user-service' // Nombre del microservicio por defecto
        DEPLOYMENT_FILE = 'user-deployment.yaml' // Archivo YAML por defecto
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials') // Credenciales de Docker Hub
        KUBERNETES_TOKEN = credentials('kubernetes-token') // Token de Kubernetes
        K8S_CLUSTER_URL = 'https://192.168.49.2:8443' // URL del clúster
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
        stage('Validate Docker Credentials') {
            steps {
                script {
                    def loginResult = sh(
                        script: """
                            echo "${DOCKER_HUB_CREDENTIALS_PSW}" | docker login -u ${DOCKER_HUB_CREDENTIALS_USR} --password-stdin
                        """,
                        returnStatus: true // Captura el código de salida del comando
                    )
                    if (loginResult == 0) {
                        echo "Docker login succeeded."
                    } else {
                        error("Docker login failed. Please check your credentials.")
                    }
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh """
                        docker buildx build --platform linux/amd64 -t mramoscli/${SERVICE_NAME}:latest .
                        docker push mramoscli/${SERVICE_NAME}:latest
                    """
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Cargar el certificado desde el archivo ya presente en el contenedor
                    sh """
                        echo "Deploying to Kubernetes..."
                        kubectl --server=${K8S_CLUSTER_URL} --token=${KUBERNETES_TOKEN} \
                        --certificate-authority=/root/ca.crt \
                        apply -f kubernetes/deployments/${DEPLOYMENT_FILE} -n production
                    """
                }
            }
        }
    }
}
