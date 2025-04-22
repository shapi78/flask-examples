pipeline {
    agent any

    stages {
        stage('Fetch AWS Secret from Vault') {
            steps {
                withCredentials([string(credentialsId: 'vault-token', variable: 'VAULT_TOKEN')]) {
                    script {
                        def vaultAddr = 'http://vault-new:8200'
                        def secretPath = 'secret/aws/data/aws/jenkins'
                        def apiUrl = "${vaultAddr}/v1/${secretPath}"

                        def response = sh(
                            script: '''curl -s --header "X-Vault-Token: $VAULT_TOKEN" --request GET "$VAULT_ADDR/v1/$SECRET_PATH"''',
                            returnStdout: true,
                            environment: [
                                "VAULT_TOKEN=$VAULT_TOKEN",
                                "VAULT_ADDR=$vaultAddr",
                                "SECRET_PATH=$secretPath"
                            ]
                        ).trim()

                        def accessKeyId = sh(
                            script: "echo '${response}' | jq -r .data.data.access_key_id",
                            returnStdout: true
                        ).trim()

                        def secretAccessKey = sh(
                            script: "echo '${response}' | jq -r .data.data.secret_access_key",
                            returnStdout: true
                        ).trim()

                        withEnv([
                            "AWS_ACCESS_KEY_ID=${accessKeyId}",
                            "AWS_SECRET_ACCESS_KEY=${secretAccessKey}"
                        ]) {
                            echo "AWS credentials fetched."
                        }
                    }
                }
            }
        }

        stage('Use AWS CLI') {
            environment {
                AWS_REGION = 'il-central-1'
            }
            steps {
                withEnv([
                    "AWS_ACCESS_KEY_ID=${env.AWS_ACCESS_KEY_ID}",
                    "AWS_SECRET_ACCESS_KEY=${env.AWS_SECRET_ACCESS_KEY}"
                ]) {
                    sh 'aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID'
                    sh 'aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY'
                    sh 'aws configure set region $AWS_REGION'
                }
            }
        }
    }
}

