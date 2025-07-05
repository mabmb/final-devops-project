pipeline {
    agent any
    environment {
        TF_DIR = "./terraform"
        ANSIBLE_DIR = "./ansible"
    }
    stages {
        stage('Terraform Init & Apply') {
            steps {
                dir("${TF_DIR}") {
                    sh 'terraform init'
                    sh 'terraform apply -auto-approve'
                }
            }
        }
        stage('Ansible Install & Deploy') {
            steps {
                script {
                    def ip = sh(script: "terraform -chdir=${TF_DIR} output -raw public_ip_address", returnStdout: true).trim()
                    sh "ANSIBLE_HOST_KEY_CHECKING=False ansible-playbook ${ANSIBLE_DIR}/install_web.yml -i '${ip},' --user azureuser --private-key ~/.ssh/id_rsa"
                }
            }
        }
        stage('Verify Deployment') {
            steps {
                script {
                    def ip = sh(script: "terraform -chdir=${TF_DIR} output -raw public_ip_address", returnStdout: true).trim()
                    sh "curl http://${ip}"
                }
            }
        }
    }
}
