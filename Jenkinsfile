pipeline {
  agent any

  environment {
    VAULT_ADDR = 'http://vault-new:8200'
  }

  stages {
    stage('Push the port to Vault') {
      steps {
        withCredentials([string(credentialsId: 'vault-token', variable: 'VAULT_TOKEN')]) {
          sh '''
            curl --silent --fail --header "X-Vault-Token: $VAULT_TOKEN" \
                 --request POST \
                 --data '{"data": {"nginx_port": "6789"}}' \
                 $VAULT_ADDR/secret/data/nginx
          '''
        }
      }
    }

    stage('Run Ansible Playbook') {
      steps {
        withCredentials([string(credentialsId: 'vault-token', variable: 'VAULT_TOKEN')]) {
          sh 'ansible-playbook site.yml --extra-vars "vault_token=$VAULT_TOKEN"'
        }
      }
    }
  }
}

