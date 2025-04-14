pipeline {
  agent any
  environment {
    VAULT_ADDR = 'http://vault-new:8200'  // Vault server URL
  }
  stages {
    stage('Fetch AWS creds from Vault') {
      steps {
        withVault([vaultSecrets: [[
          path: 'secret/aws/aws/jenkins',
          secretValues: [
            [envVar: 'AWS_ACCESS_KEY_ID', vaultKey: 'access_key_id'],
            [envVar: 'AWS_SECRET_ACCESS_KEY', vaultKey: 'secret_access_key']
          ]
        ]]]) {
          script {
            echo "AWS credentials fetched from Vault."
          }
        }
      }
    }
    stage('Use AWS CLI') {
      steps {
        sh 'aws sts get-caller-identity'
      }
    }
  }
}

