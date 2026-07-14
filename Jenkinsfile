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
                echo 'Cleaning up old container instance and starting fresh with native Docker...'
                
                // 1. Stop and remove the old container if it exists (ignoring errors if it doesn't exist yet)
                sh "docker rm -f ${CONTAINER_NAME} || true"
                
                // 2. Run the newly built container on port 8080
                sh "docker run -d --name ${CONTAINER_NAME} -p 8080:8080 ${IMAGE_NAME}:${TAG}"
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
