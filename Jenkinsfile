pipeline {
    agent any

    triggers {
        pollSCM 'H/5 * * * *'
    }
    environment {
        CI = false
        SONARSCANNER = "sonarscanner"
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
                                -Dsonar.token=${qr-momo_token}
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
                sh 'docker login -u ${USERNAME} -p ${PASSWORD} docker.io'
                sh 'docker push jaymath237/qr-momo-1'
            }
        }
    }
}
