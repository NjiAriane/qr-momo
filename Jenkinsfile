pipeline {
    agent any

    environment {
        CI = false
        SONARSCANNER = "sonarscanner"
        qr_momo_token = "my-qr-credentials" 
        DOCKERHUB_CREDENTIALS = "dockerhub-credentials" 
        SECRET_TEXT = "my-credentials" 
    }

    stages {
        stage('Run SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarscanner') {
                    script {
                        try {
                            sh """
                                ${SONARSCANNER} \
                                -Dsonar.projectKey=qr-momo \
                                -Dsonar.sources=. \
                                -Dsonar.host.url=http://localhost:9000 \
                                -Dsonar.login=${qr_momo_token} // Use QR code token
                            """
                        } catch (Exception e) {
                            error("SonarQube analysis failed: ${e.message}")
                        }
                    }
                }
            }
        }

        stage('Build') {
            steps {
                echo 'Installing Dependencies and Building'
                sh 'docker build -t qr-momo-1:${BUILD_NUMBER} .'
            }  
        }

        stage('Deployment') {
            steps {
                echo 'Deploying to Dockerhub'
                sh 'docker tag qr-momo-1:${BUILD_NUMBER} jaymath237/qr-momo-1'
                sh """
                    docker login -u ${DOCKERHUB_CREDENTIALS.username} -p ${DOCKERHUB_CREDENTIALS.password} docker.io
                """
                sh 'docker push jaymath237/qr-momo-1'
            }
        }

        // Optionally, you can use the SECRET_TEXT in another stage if needed
        stage('Use Secret Text') {
            steps {
                echo "Using secret text: ${SECRET_TEXT}" // Example usage, consider security implications
            }
        }
    }
}
