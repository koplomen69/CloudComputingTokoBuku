pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'toko_buku_online'
        DOCKERHUB_USER = 'koplomen'
        DOCKERHUB_CRED = 'dckr_pat_dGNVknLQAa-zckcBsj3vcF6Ylu0'
        ANSIBLE_SERVER = 'shaquille@172.23.72.233'
        TEAMS_WEBHOOK_URL = 'https://telkomuniversityofficial.webhook.office.com/webhookb2/a7399741-b9cc-40b7-97d8-2b36e62067e0@90affe0f-c2a3-4108-bb98-6ceb4e94ef15/JenkinsCI/597d62f353914602aaaa02f0f6318774/71d6bf8c-9c5b-49d0-b924-5e4f1db7955a/V2oQpIncItqf4g0-9DJsUfIkBu0FMHUFKzlVtujY5iyfY1'  // Ganti dengan URL webhook Teams Anda
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

            script {
                bat '''
                curl -H "Content-Type: application/json" -X POST -d "{\"text\": \"Pipeline berhasil dijalankan dan deploy sukses!\"}" %TEAMS_WEBHOOK_URL%
                '''
            }
        }
        failure {
            echo 'Pipeline gagal, periksa log untuk detail kesalahan!'

            script {
                bat '''
                curl -H "Content-Type: application/json" -X POST -d "{\"text\": \"Pipeline gagal, periksa log untuk detail kesalahan.\"}" %TEAMS_WEBHOOK_URL%
                '''
            }
        }
    }
}
