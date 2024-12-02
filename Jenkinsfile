pipeline {
    agent { label 'Linux' }
    
    stages {
        stage('Checkout Code') {
            steps {
                // Checkout the repository containing Terraform code
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false,extensions: [],userRemoteConfigs: [[url: 'https://github.com/vinayagarwal5/devops-aupostgresdb.git']]])
            }
        }
        stage('terraform'){
      environment {
        AWS_REGION = 'us-east-1' // Set the AWS region
        // Consume your Jenkins credential with the Role External ID
                TF_VAR_assume_role_external_id = credentials('jenkins-infra-external-id')
                // Provide the Role ARN. You could optionally also store this in a secret
                TF_VAR_assume_role_arn = "arn:aws:iam::985912409436:role/eec-aws-infrastructure-deployment-role"
                TF_IN_AUTOMATION = 1
                TF_CLI_ARGS = "-no-color -input=false"
    }
        steps {
                    sh 'terraform init -backend-config="role_arn=$TF_VAR_assume_role_arn" -backend-config="external_id=$TF_VAR_assume_role_external_id" -backend-config="session_name=terraform"'
                    sh 'terraform plan -out=tfplan'
                    sh 'terraform apply -auto-approve tfplan'
                    sh 'terraform destroy -auto-approve'
       }
      }
    }
    post {
        always {
            cleanWs() // Clean up workspace
        }
        success {
            echo 'RDS PostgreSQL database created successfully!'
        }
        failure {
            echo 'Failed to create RDS PostgreSQL database.'
        }
    }
}
