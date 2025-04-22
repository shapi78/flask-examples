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
                        // Fetch secret from Vault and store the response
                        def response = sh(script: '''
                            echo "Getting secret from Vault..."
                            curl -s --header "X-Vault-Token: $VAULT_TOKEN" --request GET "$VAULT_ADDR/v1/$SECRET_PATH"
                        ''', returnStdout: true).trim()

                        // Debug: Print the raw response for inspection
                        echo "Raw response from Vault: ${response}"

                        // Parse access_key_id and secret_access_key using jq
                        def accessKeyId = sh(script: "echo '${response}' | jq -r '.data.data.access_key_id'", returnStdout: true).trim()
                        def secretAccessKey = sh(script: "echo '${response}' | jq -r '.data.data.secret_access_key'", returnStdout: true).trim()

                        // Debug: Print the parsed values
                        echo "Access Key ID: ${accessKeyId}"
                        echo "Secret Access Key: ${secretAccessKey}"

                        // Set the AWS credentials as environment variables for later use
                        withEnv(["AWS_ACCESS_KEY_ID=${accessKeyId}", "AWS_SECRET_ACCESS_KEY=${secretAccessKey}"]) {
                            // You can now use the AWS CLI or other AWS-related commands
                            sh '''
                                echo "Configuring AWS CLI with the provided credentials..."
                                aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
                                aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY
                                aws configure set region il-central-1
                            '''
                        }
                    }
                }
            }
        }
    }
}

