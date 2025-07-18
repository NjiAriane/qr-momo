pipeline {
    agent any

    triggers {
        pollSCM 'H/5 * * * *'
    }
    environment {
        CI = false //do not treat errors as warnings
        SONARSCANNER = "sonarscanner"
    }

    stages {

          stage('Run SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarscanner') {
                    script {
                        sh """
                            ${SONARSCANNER} \
                            -Dsonar.projectKey=qr-momo \
                            -Dsonar.sources=. \
                            -Dsonar.host.url=http://localhost:9000 \
                            -Dsonar.token=${my_sonar_token}
                        """
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
                sh 'docker login -u ${USERNAME} -P ${PASSWORD} docker.io'
                sh 'docker push  jaymath237/qr-momo-1'
            }
        }

      

        
}
}
