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

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    echo "Deploying to Kubernetes..."

                    // Debugging untuk SCP dan SSH
                    withCredentials([sshUserPrivateKey(credentialsId: 'docker_ssh', keyFileVariable: 'SSH_KEY')]) {
                        echo "Using SCP to copy k8s-deployment.yaml to the server"
                        bat 'echo SCP Start'
                        bat 'scp -i %SSH_KEY% k8s-deployment.yaml shaquille@172.23.72.233:/home/ansible/k8s-deployment.yaml'
                        bat 'echo SCP Complete'

                        echo "Deploying to Kubernetes using kubectl..."
                        bat 'echo SSH and kubectl apply'
                        bat 'ssh -i %SSH_KEY% shaquille@172.23.72.233 "kubectl apply -f /home/ansible/k8s-deployment.yaml"'
                        bat 'echo Kubernetes deployment complete.'
                    }
                }
            }
        }
    }

   post {
        success {
            echo 'Pipeline berhasil dijalankan!'

            script {
                // Kirim notifikasi jika build sukses
                bat '''
                curl -H "Content-Type: application/json" -X POST -d "{\"text\": \"Pipeline berhasil dijalankan dan deploy sukses!\"}" %TEAMS_WEBHOOK_URL%
                '''
            }
        }
        failure {
            echo 'Pipeline gagal, periksa log untuk detail kesalahan!'

            script {
                // Kirim notifikasi jika build gagal
                bat '''
                curl -H "Content-Type: application/json" -X POST -d "{\"text\": \"Pipeline gagal, periksa log untuk detail kesalahan.\"}" %TEAMS_WEBHOOK_URL%
                '''
            }
        }
    }
}
