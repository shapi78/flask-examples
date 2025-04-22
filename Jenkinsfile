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
                    script {
                        // Fetch the secret from Vault using curl
                        def response = sh(script: """
                            echo "Getting secret from Vault..."
                            curl -s --header "X-Vault-Token: $VAULT_TOKEN" --request GET "$VAULT_ADDR/v1/$SECRET_PATH"
                        """, returnStdout: true).trim()

                        // Log the raw response (for debugging)
                        echo "Vault raw response: ${response}"

                        // Parse access key and secret key using jq
                        def accessKeyId = sh(script: "echo '${response}' | jq -r '.data.data.access_key_id'", returnStdout: true).trim()
                        def secretAccessKey = sh(script: "echo '${response}' | jq -r '.data.data.secret_access_key'", returnStdout: true).trim()

                        // Set the parsed values as environment variables for use later
                        env.AWS_ACCESS_KEY_ID = accessKeyId
                        env.AWS_SECRET_ACCESS_KEY = secretAccessKey

                        // Log the fetched values for debugging (Don't log sensitive values in production)
                        echo "Fetched AWS Access Key: ${env.AWS_ACCESS_KEY_ID}"
                        echo "Fetched AWS Secret Key: ${env.AWS_SECRET_ACCESS_KEY}"
                    }
                }
            }
        }

        stage('Use AWS CLI') {
            steps {
                script {
                    // Example: Using the AWS keys to configure AWS CLI (for demonstration)
                    sh """
                    aws configure set aws_access_key_id ${env.AWS_ACCESS_KEY_ID}
                    aws configure set aws_secret_access_key ${env.AWS_SECRET_ACCESS_KEY}
                    aws configure set region us-east-1
                    aws sts get-caller-identity
                    """
                }
            }
        }
    }
}

