pipeline {
    agent any

    stages {
        stage('Fetch AWS Secret Key from Vault') {
            steps {
                withVault(
                    configuration: [vaultCredentialId: 'vault-token'],
                    vaultSecrets: [
                        [path: 'secret/data/aws/aws/jenkins', secretValues: [
                            [envVar: 'AWS_SECRET', vaultKey: 'secret-key']
                        ]]
                    ]
                ) {
                    // You can now use $AWS_SECRET here
                    sh 'echo "Fetched secret: $AWS_SECRET"'
                }
            }
        }

        stage('Use AWS CLI') {
            steps {
                sh 'aws configure set aws_secret_access_key $AWS_SECRET'
                // continue with your AWS CLI steps...
            }
        }
    }
}

