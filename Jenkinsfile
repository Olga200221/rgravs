pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    def image = docker.build("architecture_image:${BUILD_NUMBER}")
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    // Проверяем, существует ли контейнер, и останавливаем его
                    sh '''
                    if docker ps -a --format '{{.Names}}' | grep -q '^architecture_container$'; then
                        docker stop architecture_container || true
                        docker rm architecture_container || true
                    fi
                    '''
                    // Запускаем новый контейнер
                    sh "docker run -d --name architecture_container -p 8081:8080 architecture_image:${BUILD_NUMBER}"
                }
            }
        }
    }
    post {
        always {
            script {
                // Очищаем неиспользуемые образы
                sh 'docker image prune -f'
            }
        }
    }
}