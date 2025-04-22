pipeline {
    agent any

    stages {
        stage('Fetch AWS Secret Key from Vault') {
            steps {
                withVault(
                    configuration: [vaultCredentialId: 'vault-token'],  // Make sure this credential ID is correct
                    vaultSecrets: [
                        [path: 'secret/aws/aws/jenkins', secretValues: [
                            [envVar: 'AWS_ACCESS_KEY_ID', vaultKey: 'access_key_id'],
                            [envVar: 'AWS_SECRET_ACCESS_KEY', vaultKey: 'secret_access_key']
                        ]]
                    ]
                ) {
                    // You can now use $AWS_ACCESS_KEY_ID and $AWS_SECRET_ACCESS_KEY here
                    sh 'echo "Fetched AWS Access Key: $AWS_ACCESS_KEY_ID"'
                    sh 'echo "Fetched AWS Secret Key: $AWS_SECRET_ACCESS_KEY"'
                }
            }
        }

        stage('Use AWS CLI') {
            steps {
                sh 'aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID'
                sh 'aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY'
                // Continue with other AWS CLI steps...
            }
        }
    }
}

