pipeline {
    agent any
    
    triggers {
         pollSCM '* * * * *'
    }
    environment {
        CI = false          // do not treat warnings as errors
    }
    stages {
        stage('Run SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'sonar-jenkins-token', variable: 'SONARQUBE')]) {
                    sh '''
                        sonar-scanner \
                          -Dsonar.projectKey=qr-momo-code-analysis \
                          -Dsonar.sources=. \
                          -Dsonar.host.url=http://sonarqube:9000 \
                          -Dsonar.login=$SONARQUBE
                    '''
                }   
            }
        }
        
        stage('Dockerizing') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'docker login -u $USERNAME -p $PASSWORD'
    // Your Docker commands using the environment variables
                    sh 'docker build -t qrmomojenk:v2 .'
                    sh 'docker tag qrmomojenk:v2 ariana1/qrmomojenk:v2'
                    sh 'docker push ariana1/qrmomojenk:v2'
                }
            } 
        }         
    }    
}
