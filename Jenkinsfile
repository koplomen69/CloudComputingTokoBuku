pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'toko_buku_online'
        DOCKERHUB_USER = 'koplomen'
        DOCKERHUB_CRED = 'dckr_pat_dGNVknLQAa-zckcBsj3vcF6Ylu0'
        ANSIBLE_SERVER = 'shaquille@172.23.72.233'
    }

    stages {
        stage('Checkout Kode Sumber') {
            steps {
                git branch: 'main', url: 'https://github.com/koplomen69/CloudComputingTokoBuku.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build docker image (Windows menggunakan 'bat' untuk perintah shell)
                    bat 'docker build -t %DOCKERHUB_USER%/%DOCKER_IMAGE% .'
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    echo "Logging into DockerHub..."
                    withCredentials([string(credentialsId: "dockerhub_token", variable: 'DOCKERHUB_PASS')]) {
                        bat 'docker login -u %DOCKERHUB_USER% -p %DOCKERHUB_PASS%'
                        bat 'docker info'
                    }

                    echo "Pushing Docker image to DockerHub..."
                    bat 'docker push %DOCKERHUB_USER%/%DOCKER_IMAGE%'
                    echo "Push to DockerHub complete."
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline berhasil dijalankan!'
        }
        failure {
            echo 'Pipeline gagal, periksa log untuk detail kesalahan!'
        }
    }
}
