pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Maven Build & Test') {
            steps {
                sh 'mvn clean test package'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t devops-cicd-demo:latest .'
            }
        }
        stage('Docker Hub Push') {
    steps {
        withCredentials([usernamePassword(
            credentialsId: 'dockerhub-credentials',
            usernameVariable: 'DOCKERHUB_USERNAME',
            passwordVariable: 'DOCKERHUB_TOKEN'
        )]) {
            sh '''
                echo "$DOCKERHUB_TOKEN" | docker login -u "$DOCKERHUB_USERNAME" --password-stdin
                docker tag devops-cicd-demo:latest "$DOCKERHUB_USERNAME/devops-cicd-demo:latest"
                docker push "$DOCKERHUB_USERNAME/devops-cicd-demo:latest"
                docker logout
            '''
        }
    }
}

        stage('Deploy Container') {
            steps {
                sh '''
                    docker stop devops-cicd-demo-container || true
                    docker rm devops-cicd-demo-container || true

                    docker run -d \
                      --name devops-cicd-demo-container \
                      -p 8081:8080 \
                      devops-cicd-demo:latest
                '''
            }
        }
    }

    post {
        success {
            echo 'CI/CD Pipeline completed successfully!'
        }

        failure {
            echo 'CI/CD Pipeline failed!'
        }
    }
    //ci/cd poll scm test vishnu 
}
