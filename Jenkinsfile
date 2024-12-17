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

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    echo "Deploying to Kubernetes..."

                    // Debugging untuk SCP dan SSH
                    withCredentials([sshUserPrivateKey(credentialsId: 'docker_ssh_key', keyFileVariable: 'SSH_KEY')]) {
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
        }
        failure {
            echo 'Pipeline gagal, periksa log untuk detail kesalahan!'
        }
    }
}
