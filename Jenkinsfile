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
                    // Login ke DockerHub dan push image
                    withCredentials([string(credentialsId: "dckr_pat_dGNVknLQAa-zckcBsj3vcF6Ylu0", variable: 'DOCKERHUB_PASS')]) {
                        bat 'echo %DOCKERHUB_USER% %DOCKERHUB_PASS%'
                        bat 'docker login -u %DOCKERHUB_USER% -p %DOCKERHUB_PASS%'

                        // Periksa login apakah berhasil
                        bat 'docker info'

                        bat 'docker push %DOCKERHUB_USER%/%DOCKER_IMAGE%'
                    }
                }
            }
        }


        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Copy deployment file to Ansible server
                    bat 'scp k8s-deployment.yaml %ANSIBLE_SERVER%:/home/ansible/k8s-deployment.yaml'

                    // Deploy to Kubernetes using Ansible
                    bat 'ssh %ANSIBLE_SERVER% "kubectl apply -f /home/ansible/k8s-deployment.yaml"'
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
