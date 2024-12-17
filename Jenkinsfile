pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'toko_buku_online:latest'
        DOCKERHUB_USER = 'koplomen'
        DOCKERHUB_CRED = 'dckr_pat_dGNVknLQAa-zckcBsj3vcF6Ylu0'
        ANSIBLE_SERVER = 'user@192.168.1.50'
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
                    // Build docker image
                    sh 'docker build -t ${DOCKERHUB_USER}/${DOCKER_IMAGE} .'
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    // Login to DockerHub and push the image
                    withCredentials([string(credentialsId: "${DOCKERHUB_CRED}", variable: 'DOCKERHUB_PASS')]) {
                        sh 'docker login -u ${DOCKERHUB_USER} -p ${DOCKERHUB_PASS}'
                        sh 'docker push ${DOCKERHUB_USER}/${DOCKER_IMAGE}'
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Copy deployment file to Ansible server
                    sh 'scp k8s-deployment.yaml ${ANSIBLE_SERVER}:/home/ansible/k8s-deployment.yaml'

                    // Deploy to Kubernetes using Ansible
                    sh 'ssh ${ANSIBLE_SERVER} "kubectl apply -f /home/ansible/k8s-deployment.yaml"'
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
