pipeline {
    agent any
    
    environment {
        // Disable SSH host key checking to avoid verification prompt
        ANSIBLE_HOST_KEY_CHECKING = 'False'
    }

    stages {
        stage('Clean Workspace') {
            steps {
                script {
                    // Clean the workspace before the job starts
                    deleteDir()
                }
            }
        }
        stage('checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/amaanraaz/nginx-ansible_role.git'
            }
        }
        stage('Run GitLeaks') {
            steps {
                script {
                    // sh 'gitleaks detect --source=. --report-format=json --report-path=gitleaks_report.json' 
                    // new format
                    sh 'gitleaks git . --report-format=json --report-path=gitleaks_report.json'
                }
            }
        }
        stage('YAML Lint') {
            steps {
                script {
                    sh '''
                        yamllint playbook.yml aws_ec2.yml nginx-management/**/*.yml
                    '''
                }
            }
        }
        
        stage('playbook exec'){
            steps{
                ansiblePlaybook credentialsId: '371b4f37-1f01-47c7-aac3-016ee12aae56', inventory: 'aws_ec2.yml', 
                playbook: 'playbook.yml'
            }
        }
    }
}
