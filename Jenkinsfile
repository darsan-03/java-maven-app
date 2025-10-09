pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "ashokraji/tomcat"
        DOCKER_TAG = "9.0-${BUILD_NUMBER}"  // dynamic tag — good
        SONARQUBE_URL = 'http://13.221.128.189:9000'  // SonarQube URL — good
        SONARQUBE_TOKEN = credentials('sonarqube-token')  // good use of credentials binding
    }

    tools {
        maven 'maven'  // make sure this matches your Jenkins global tool name exactly
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Ashokraji5/java-maven-app.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Building with Maven...'
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Analysis') {
    steps {
        script {
            echo 'Running SonarQube analysis...'
            sh """
                mvn sonar:sonar \
                -Dsonar.projectKey=my-project-key \
                -Dsonar.host.url=${SONARQUBE_URL} \
                -Dsonar.login=${SONARQUBE_TOKEN}
            """
        }
    }
}


        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
                    sh 'echo $DOCKERHUB_PASS | docker login -u $DOCKERHUB_USER --password-stdin'
                    sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline succeeded 🎉'
        }
        failure {
            echo 'Pipeline failed ❌'
        }
    }
}
