pipeline {
    agent { label 'maven' }

    environment {
        DOCKER_IMAGE = "ashokraji/tomcat"
        DOCKER_TAG = "9.0-${BUILD_NUMBER}"
        SONARQUBE_URL = 'http://34.207.207.43:9000'  // updated SonarQube IP
        SONARQUBE_TOKEN = credentials('sonarqube-token')
    }

    tools {
        maven 'maven'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Ashokraji5/java-maven-app.git', credentialsId: 'github-credentials'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    sh """
                        mvn sonar:sonar \
                        -Dsonar.projectKey=my-project-key \
                        -Dsonar.host.url=${SONARQUBE_URL} \
                        -Dsonar.login=${SONARQUBE_TOKEN}
                    """
                }
