pipeline {
    agent any

    environment {
        IMAGE = "ehurwitz818/flask-hello-world"
        TAG   = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Code Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Docker Build+Tag') {
            steps {
                sh "docker build -t ${IMAGE}:${TAG} -t ${IMAGE}:latest ."
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
                    sh 'echo $DH_PASS | docker login -u $DH_USER --password-stdin'
                    sh "docker push ${IMAGE}:${TAG}"
                    sh "docker push ${IMAGE}:latest"
                }
            }
        }

        stage('Deploy') {
            steps {
                sh "sed -i 's|IMAGE_PLACEHOLDER|${IMAGE}:${TAG}|' deployment.yaml"
                sh "kubectl apply -f deployment.yaml"
            }
        }
    }
}
