pipeline {
    agent any

    environment {
        IMAGE_NAME = "app"
        CONTAINER_NAME = "cont-7"
        HOST_PORT = "8003"
        APP_PORT = "8080"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Maven Build') {
            steps {
                sh """
                export PATH=/opt/maven/bin:$PATH
                mvn clean package -DskipTests
                """
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:v${BUILD_NUMBER} ."
            }
        }

        stage('Stop Old Container') {
            steps {
                sh """
                if [ \$(docker ps -aq -f name=${CONTAINER_NAME}) ]; then
                    docker rm -f ${CONTAINER_NAME}
                fi
                """
            }
        }

        stage('Run New Container') {
            steps {
                sh """
                docker run -d --name ${CONTAINER_NAME} \
                -p ${HOST_PORT}:${APP_PORT} \
                ${IMAGE_NAME}:v${BUILD_NUMBER}
                """
            }
        }
    }

    post {
        success {
            echo "Deployment successful!"
            sh "docker ps"
        }
        failure {
            echo "Deployment failed!"
        }
    }
}
