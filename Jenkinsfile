
pipeline {
    agent any

    environment {
        CI = false
        SONAR_PROJECT_KEY = "qr-momo"
    }

    stages {
        stage('Run SonarQube Analysis') {
            environment {
                qr_momo_token = credentials('my-qr-credentials')
            }
            steps {
                withSonarQubeEnv('sonarscanner') {
                    script {
                        def scannerHome = tool 'sonarscanner'
                        try {
                            sh """
                                ${scannerHome}/bin/sonar-scanner \
                                -Dsonar.projectKey=${env.SONAR_PROJECT_KEY} \
                                -Dsonar.sources=. \
                                -Dsonar.host.url=${env.SONAR_HOST_URL} \
                                -Dsonar.login=${qr_momo_token}
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
            environment {
                DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
            }
            steps {
                echo 'Deploying to DockerHub'
                sh "docker tag qr-momo-1:${BUILD_NUMBER} jaymath237/qr-momo-1:${BUILD_NUMBER}"
                sh """
                    echo "${DOCKERHUB_CREDENTIALS_PSW}" | docker login -u "${DOCKERHUB_CREDENTIALS_USR}" --password-stdin
                """
                sh "docker push jaymath237/qr-momo-1:${BUILD_NUMBER}"
            }
        }
    }
}
