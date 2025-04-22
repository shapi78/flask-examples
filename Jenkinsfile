pipeline {
    agent any

    environment {
        VAULT_ADDR = 'http://vault-new:8200'
        SECRET_PATH = 'secret/aws/data/aws/jenkins'
    }

    stages {
        stage('Fetch AWS Secret from Vault') {
            steps {
                withCredentials([string(credentialsId: 'vault-token', variable: 'VAULT_TOKEN')]) {
                    sh '''
                    echo "Getting secret from Vault..."
                    curl -s --header "X-Vault-Token: $VAULT_TOKEN" \
                        --request GET "$VAULT_ADDR/v1/$SECRET_PATH"
                    '''
                }
            }
        }
    }
}

