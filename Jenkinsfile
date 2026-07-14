pipeline {
    agent any

    environment {
        IMAGE_NAME = "student-ecommerce"
        TAG = "latest"
    }

    stages {
        stage('Pull Repository') {
            steps {
                checkout scm
            }
        }

        stage('Verify & Build Jar') {
            steps {
                echo 'Running native Maven packaging build verify steps...'
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Container Image') {
            steps {
                echo 'Building updated container image layers...'
                sh "docker build -t ${IMAGE_NAME}:${TAG} ."
            }
        }

        stage('Deploy Live Container') {
            steps {
                echo 'Re-spawning application layers via Docker Compose...'
                sh 'docker-compose down'
                sh 'docker-compose up -d --build'
            }
        }
    }

    post {
        success {
            echo 'Pipeline finished. E-commerce application structure successfully deployed.'
        }
        failure {
            echo 'Build failed. Check the Jenkins console output log for errors.'
        }
    }
}
