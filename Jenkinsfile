pipeline {
    agent any

    environment {
        VAULT_ADDR = 'http://vault-new:8200'
        SECRET_PATH = 'secret/aws/data/aws/jenkins'
    }

    stages {
        stage('Fetch AWS Secret (manual)') {
            steps {
                withCredentials([string(credentialsId: 'vault-token', variable: 'VAULT_TOKEN')]) {
                    sh '''
                    echo "Getting secret from Vault..."
                    response=$(curl -s --header "X-Vault-Token: $VAULT_TOKEN" \
                        --request GET "$VAULT_ADDR/v1/$SECRET_PATH")

                    echo "Vault raw response: $response"

                    # Basic parsing using grep/sed (if jq is not available)
                    AWS_SECRET=$(echo "$response" | sed -n 's/.*"secret_access_key"[ ]*:[ ]*"\([^"]*\)".*/\\1/p')

                    echo "Fetched secret: $AWS_SECRET"

                    # Export for the session
                    export AWS_SECRET=$AWS_SECRET

                    # Optional: Save it to a file or a temp var for later
                    echo "AWS_SECRET=$AWS_SECRET" > aws_secret.env
                    '''
                }
            }
        }

        stage('Use AWS CLI') {
            steps {
                sh '''
                # Load secret from file (if needed)
                source aws_secret.env

                # Example AWS usage
                aws configure set aws_secret_access_key "$AWS_SECRET"
                aws configure list
                '''
            }
        }
    }
}

