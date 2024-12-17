pipeline {
    agent any

    environment {
        MYSQL_ROOT_PASSWORD = credentials('mysql-root-password') // Tambahkan ke Jenkins Credentials
        MYSQL_USER = credentials('mysql-user') // Tambahkan ke Jenkins Credentials
        MYSQL_PASSWORD = credentials('mysql-password') // Tambahkan ke Jenkins Credentials
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials') // Jika ingin push ke Docker Hub
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out the code from GitHub...'
                git branch: 'main', url: 'https://github.com/koplomen69/CloudComputingTokoBuku.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                echo 'Building Docker Images...'
                sh '''
                    docker-compose down
                    docker-compose build
                '''
            }
        }

        stage('Run Containers') {
            steps {
                echo 'Starting Docker Containers...'
                sh '''
                    docker-compose up -d
                '''
            }
        }

        stage('Run Tests') {
            steps {
                echo 'Running Laravel tests...'
                sh '''
                    docker exec toko_online php artisan migrate --force
                    docker exec toko_online php artisan test
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying Application...'
                sh '''
                    docker exec toko_online php artisan config:cache
                    docker exec toko_online php artisan route:cache
                '''
            }
        }

    }

    post {
        always {
            echo 'Pipeline finished. Cleaning up Docker containers...'
            sh 'docker-compose down'
        }
        success {
            echo 'Build succeeded. Application deployed successfully!'
        }
        failure {
            echo 'Build failed. Cleaning up Docker containers...'
            sh 'docker-compose down'
        }
    }
}
