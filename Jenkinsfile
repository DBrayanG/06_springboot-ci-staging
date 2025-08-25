pipeline {
    agent any
    environment {
        STAGING_SERVER = 'user@192.168.0.16' // Cambia por tu usuario y la IP real de tu VM
        ARTIFACT_NAME = 'demo-0.0.1-SNAPSHOT.jar' // Cambia si tu JAR tiene otro nombre
        DEPLOY_PATH = '/home/user/staging/' // Cambia si usas otra ruta
        HEALTH_URL = 'http://192.168.0.16:8080/health' // Cambia por la IP real de tu VM
    }
    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/DBrayanG/06_springboot-ci-staging.git' // Cambia por tu repo real
            }
        }
        stage('Code Quality') {
            steps {
                sh 'mvn checkstyle:check'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Code Coverage') {
            steps {
                sh 'mvn jacoco:report'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Deploy to Staging') {
            steps {
                sh "scp target/${ARTIFACT_NAME} ${STAGING_SERVER}:${DEPLOY_PATH}"
                sh "ssh ${STAGING_SERVER} 'nohup java -jar ${DEPLOY_PATH}${ARTIFACT_NAME} > /dev/null 2>&1 &'"
            }
        }
        stage('Validate Deployment') {
            steps {
                sh 'sleep 10'
                sh "curl --fail ${HEALTH_URL}"
            }
        }
    }
}