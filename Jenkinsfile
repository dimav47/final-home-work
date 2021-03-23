pipeline {

  agent {

    docker {
      image 'dimav47/terraformansible'
      args  '-v /var/run/docker.sock:/var/run/docker.sock -u 0:0'
      registryCredentialsId 'fdb54f96-c857-4a66-b6e2-fc0e8064b437'
    }
  }

  stages {

    stage('Clone Terraform manifest and Ansible Playbook form GitHub') {
      steps{
        git 'https://github.com/mas0lik/terraform-docker-v2.git'
      }
    }

    stage('Copy GCP authentication json to workspace on Jenkins agent') {
      steps {
        withCredentials([file(credentialsId: '46558e95-4951-4c57-bd1f-5918eba9044a', variable: 'gcp_auth')]) {
          sh 'cp \$gcp_auth jenk-gcp.json'
        }
      }
    }


    stage('Encrypt Dockerhub password with Ansible Vault') {
      steps {
        withCredentials([string(credentialsId: '01becf7b-7085-4ed1-889d-8d2da87b0e05', variable: 'encrypt')]) {
          sh 'ansible-vault encrypt_string $encrypt --name dockerhub_token --vault-password-file vault_pass >>./roles/dockerhub_connect/defaults/main.yml'
        }
      }
    }

    stage('Execute Terraform Init, Plan and Apply') {
      steps {
        sh 'terraform init'
        sh 'terraform plan'
        sh 'terraform apply -auto-approve'
      }
    }

  }
}