pipeline {
    agent any
    environment {
        ARM_CLIENT_ID       = credentials('azure-client-id')
        ARM_CLIENT_SECRET   = credentials('azure-client-secret')
        ARM_SUBSCRIPTION_ID = credentials('azure-subscription-id')
        ARM_TENANT_ID       = credentials('azure-tenant-id')
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/uday3005/ansible.git'
            }
        }
        stage('Generate Inventory') {
            steps {
                sh '''
                    # Query Azure for VM IPs
                    az vm list-ip-addresses -o json \
                      | jq -r '.[] | .virtualMachine.network.publicIpAddresses[].ipAddress' > hosts.txt

                    echo "[azure_vms]" > inventory.ini
                    cat hosts.txt >> inventory.ini

                    echo "Generated inventory:"
                    cat inventory.ini
                '''
            }
        }
        stage('Run Ansible Playbook') {
            steps {
                sh '''
                    ansible-playbook -i inventory.ini create_vm.yaml
                '''
            }
        }
    }
}
