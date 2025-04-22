script {
    // The Vault token is now available in the 'VAULT_TOKEN' environment variable

    // Define Vault API endpoint and the secret path
    def vaultAddr = 'http://vault-new:8200' // Replace with your Vault address if different
    def secretPath = 'secret/aws/data/aws/jenkins'

    // Construct the Vault API URL for reading a secret
    def apiUrl = "${vaultAddr}/v1/${secretPath}"

    echo "Getting secret from Vault..." // Keep this for informational purposes

    // Execute the curl command to read the secret and capture the output
    def response = sh(script: """
        curl \
          -s \
          --header "X-Vault-Token: $VAULT_TOKEN" \
          --request GET \
          "${apiUrl}"
    """, returnStdout: true).trim()

    echo "Raw response from Vault: ${response}" // For debugging

    // Extract the access key ID using jq
    env.AWS_ACCESS_KEY_ID = sh(script: "echo '${response}' | jq -r .data.data.access_key_id", returnStdout: true).trim()

    // Extract the secret access key using jq
    env.AWS_SECRET_ACCESS_KEY = sh(script: "echo '${response}' | jq -r .data.data.secret_access_key", returnStdout: true).trim()

    echo "Successfully fetched AWS Access Key ID: ${env.AWS_ACCESS_KEY_ID}"
    echo "Successfully fetched AWS Secret Access Key: ${env.AWS_SECRET_ACCESS_KEY}"
}
