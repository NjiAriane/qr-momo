pipeline {
    agent any

    environment {
        CI = false
    }

    stages {
        stage('Build') {
            steps {
                echo 'Installing Dependencies and Building'
                sh 'docker build -t qr-momo-1:${BUILD_NUMBER} .'
            }
        }

        stage('Deployment') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    echo 'Deploying to DockerHub'
                    sh "docker tag qr-momo-1:${BUILD_NUMBER} jaymath237/qr-momo-1:${BUILD_NUMBER}"
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    """
                    sh "docker push NjiAriane/qr-momo-1:${BUILD_NUMBER}"
                }
            }
        }
    }
}  
