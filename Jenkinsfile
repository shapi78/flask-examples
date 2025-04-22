pipeline {
    agent any

    environment {
        VAULT_ADDR = 'http://vault-new:8200'     
        VAULT_TOKEN = credentials('vault-token')    
        SECRET_PATH = 'secret/data/aws/aws/jenkins' 
    }

    stages {
        stage('Fetch AWS Secret from Vault )') {
            steps {
                sh '''
                echo "Getting secret from Vault..."

                # Fetch the secret from Vault KV v2 API
                response=$(curl -s --header "X-Vault-Token: $VAULT_TOKEN" \
                    --request GET "$VAULT_ADDR/v1/$SECRET_PATH")

                echo "Vault raw response: $response"

                # Parse secret_access_key from the JSON using jq
                export AWS_SECRET=$(echo "$response" | jq -r '.data.data.secret_access_key')

                # Confirm it was pulled
                echo "Fetched secret key: $AWS_SECRET"

                # Export for downstream steps
                echo "AWS_SECRET=$AWS_SECRET" >> $BASH_ENV
                '''
            }
        }

        stage('Use AWS CLI') {
            steps {
                sh '''
                aws configure set aws_secret_access_key "$AWS_SECRET"
                aws configure list
                '''
            }
        }
    }
}

