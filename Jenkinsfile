pipeline {
    agent any
    environment {
        GITHUB_CREDENTIALS = credentials('github-token')
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/koplomen69/CloudComputingTokoBuku.git'
            }
        }
        stage('Build') {
            steps {
                echo 'Building...'
                sh 'make build' // Menjalankan perintah build
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
                sh 'make test' // Menjalankan pengujian otomatis
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
                sh './deploy.sh' // Menjalankan skrip deployment
            }
        }
    }
    post {
        always {
            echo 'Pipeline selesai!'
        }
        success {
            echo 'Build sukses!'
        }
        failure {
            echo 'Build gagal!'
        }
    }
}
