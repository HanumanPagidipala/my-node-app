pipeline {
    agent any

    stages {

        stage('Checkout from GitHub') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/Pranay-Medipally/my-node-app.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t my-node-app:${BUILD_NUMBER} .
                docker tag my-node-app:${BUILD_NUMBER} pranaymedipally/my-node-app:latest
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push pranaymedipally/my-node-app:latest'
            }
        }

        stage('Start Minikube if not running') {
            steps {
                sh '''
                if ! minikube status | grep -q "apiserver: Running"; then
                    echo "Minikube is not running. Starting now..."
                    minikube start --driver=docker --memory=2048 --cpus=2
                fi
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                # Load latest image into Minikube
                minikube image load pranaymedipally/my-node-app

                # Apply manifests
                minikube kubectl -- apply -f k8s/deployment.yaml
                minikube kubectl -- apply -f k8s/service.yaml
                minikube service my-node-app-service
                '''
            }
        }
    }
}
