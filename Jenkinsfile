pipeline {
    agent any

    environment {
        IMAGE_NAME = "student-ecommerce"
        TAG = "latest"
        CONTAINER_NAME = "student-ecommerce-app"
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
                echo 'Cleaning up old container instance and starting fresh on port 8081...'
                
                // 1. Force stop/remove old container instance if it exists
                sh "docker rm -f ${CONTAINER_NAME} || true"
                
                // 2. Bind external port 8081 to internal container port 8080
                sh "docker run -d --name ${CONTAINER_NAME} -p 8081:8080 ${IMAGE_NAME}:${TAG}"
            }
        }
    }

    post {
        success {
            echo 'Pipeline finished. E-commerce application successfully deployed on port 8081.'
        }
        failure {
            echo 'Build failed. Check the Jenkins console output log for errors.'
        }
    }
}
