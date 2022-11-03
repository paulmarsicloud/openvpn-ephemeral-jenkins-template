pipeline {
    agent any
    stages {
        stage('Create VPN') {
            environment {
                TF_VAR_public_ip = <REPLACE ME>
                AWS_DEFAULT_REGION = 'us-east-1'
            }
            agent {
                docker { image 'paulmarsicloud/terragrunt-awscli:latest' }
            }
            input {
                message "Proceed?"
                ok "Yes"
            }
            steps {
                sh 'terragrunt init --terragrunt-non-interactive; terragrunt apply -auto-approve'
                script {
                    env.BUCKET = sh(script: 'terragrunt output -raw bucket', returnStdout: true)
                }
            }
        }
        stage('Obtain openvpn.ovpn file') {
            steps {
                sh('aws s3 cp s3://$BUCKET/openvpn.ovpn .')
            }
        post {
        always {
            archiveArtifacts artifacts: 'openvpn.ovpn', fingerprint: true
        }
    }
        }
        stage('Destroy VPN') {
            environment {
                TF_VAR_public_ip = <REPLACE ME>
                AWS_DEFAULT_REGION = 'us-east-1'
            }
            agent {
                docker { image 'paulmarsicloud/terragrunt-awscli:latest' }
            }
            input {
                message "Proceed?"
                ok "Yes"
            }
            steps {
                sh 'terragrunt init --terragrunt-non-interactive; terragrunt destroy -auto-approve'
            }
        }
    }

}