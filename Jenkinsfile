pipeline {
    agent any

    environment {
        CI = false
        SONAR_PROJECT_KEY = "qr-momo"
        SONAR_HOST_URL = "http://192.168.1.230:9000" // 👈 Replace with your SonarQube server IP
    }

    stages {
        stage('Run SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'my-qr-credentials', variable: 'SONAR_TOKEN')]) {
                    withSonarQubeEnv('sonarscanner') {
                        script {
                            def scannerHome = tool 'sonarscanner'
                            try {
                                sh """
                                    ${scannerHome}/bin/sonar-scanner \
                                    -Dsonar.projectKey=${env.SONAR_PROJECT_KEY} \
                                    -Dsonar.sources=. \
                                    -Dsonar.host.url=${env.SONAR_HOST_URL} \
                                    -Dsonar.login=$SONAR_TOKEN
                                """
                            } catch (Exception e) {
                                error("SonarQube analysis failed: ${e.message}")
                            }
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
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    echo 'Deploying to DockerHub'
                    sh "docker tag qr-momo-1:${BUILD_NUMBER} jaymath237/qr-momo-1:${BUILD_NUMBER}"
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    """
                    sh "docker push jaymath237/qr-momo-1:${BUILD_NUMBER}"
                }
            }
        }
    }
}

                  
