pipeline {
    agent { label 'maven' }

    environment {
        DOCKER_IMAGE = "ashokraji/tomcat"
        DOCKER_TAG = "9.0-${BUILD_NUMBER}"
        SONARQUBE_URL = 'http://52.90.161.136:9000'         // ✅ Updated SonarQube IP
        SONARQUBE_TOKEN = credentials('sonarqube-token')    // 🔐 Jenkins secret text
    }

    tools {
        maven 'maven'  // Make sure 'maven' is defined under Jenkins tools
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Ashokraji5/java-maven-app.git',
                    credentialsId: 'github-credentials'    // 🔐 GitHub credentials in Jenkins
            }
        }

        stage('Build with Maven') {
            steps {
                echo "🔧 Building the project with Maven..."
                sh 'mvn clean package -DskipTests -e -X'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo "🔍 Running SonarQube analysis..."
                sh """
                    mvn sonar:sonar \
                    -Dsonar.projectKey=my-project-key \
                    -Dsonar.host.url=${SONARQUBE_URL} \
                    -Dsonar.login=${SONARQUBE_TOKEN}
                """
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "🐳 Building Docker image..."
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                echo "📤 Pushing Docker image to DockerHub..."
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
