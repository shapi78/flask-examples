pipeline {
    agent any

    environment {
        VAULT_ADDR = 'http://vault-new:8200'
        VAULT_TOKEN = credentials('vault_token')
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Fetch AWS Secret from Vault') {
            steps {
                script {
                    def response = sh(script: '''
                        curl -s --header "X-Vault-Token: ${VAULT_TOKEN}" --request GET ${VAULT_ADDR}/v1/secret/data/aws/aws/jenkins
                    ''', returnStdout: true)

                    echo "Vault raw response: ${response}"

                    def accessKeyId = sh(script: "echo '${response}' | jq -r '.data.data.access_key_id'", returnStdout: true).trim()
                    def secretAccessKey = sh(script: "echo '${response}' | jq -r '.data.data.secret_access_key'", returnStdout: true).trim()

                    echo "Access Key ID: ${accessKeyId}"
                    echo "Secret Access Key: ${secretAccessKey}"

                    env.AWS_ACCESS_KEY_ID = accessKeyId
                    env.AWS_SECRET_ACCESS_KEY = secretAccessKey
                }
            }
        }

        stage('Use AWS CLI') {
            steps {
                script {
                    sh '''
                        echo "Configuring AWS CLI with the fetched credentials"
                        aws configure set aws_access_key_id ${AWS_ACCESS_KEY_ID}
                        aws configure set aws_secret_access_key ${AWS_SECRET_ACCESS_KEY}
                        aws configure set region il-central-1
                    '''
                }
            }
        }
    }
}

