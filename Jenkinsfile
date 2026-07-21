pipeline {
    agent any
    
    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timeout(time: 1, unit: 'HOURS')
        timestamps()
    }
    
    environment {
        ANSIBLE_VAULT_ID = credentials('ansible-vault-password')
        GIT_REPO = 'https://github.com/shivashankar44/ansible_new.git'
        GIT_BRANCH = 'main'
        INVENTORY_FILE = 'iniventory.ini'
        PLAYBOOK_LAB5 = 'lab5.yaml'
        PLAYBOOK_LAB6 = 'lab6.yaml'
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo '========== Checking out Ansible repository =========='
                git branch: "${GIT_BRANCH}", 
                    url: "${GIT_REPO}",
                    credentialsId: 'github-credentials'
                sh 'pwd && ls -la'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                echo '========== Installing Ansible and dependencies =========='
                sh '''
                    sudo dnf -y install ansible-core
                    pip install --upgrade pip
                    pip install ansible-lint
                    pip install yamllint
                    ansible --version
                '''
            }
        }
        
        stage('Validate Syntax') {
            steps {
                echo '========== Validating Ansible playbook syntax =========='
                sh '''
                    echo "Validating ${PLAYBOOK_LAB5}..."
                    ansible-playbook --syntax-check ${PLAYBOOK_LAB5}
                    
                    echo "Validating ${PLAYBOOK_LAB6}..."
                    ansible-playbook --syntax-check ${PLAYBOOK_LAB6}
                '''
            }
        }
        
        stage('Lint Playbooks') {
            steps {
                echo '========== Running Ansible Lint =========='
                sh '''
                    echo "Linting ${PLAYBOOK_LAB5}..."
                    ansible-lint ${PLAYBOOK_LAB5} || true
                    
                    echo "Linting ${PLAYBOOK_LAB6}..."
                    ansible-lint ${PLAYBOOK_LAB6} || true
                '''
            }
        }
        
        stage('Validate Inventory') {
            steps {
                echo '========== Validating Inventory =========='
                sh '''
                    echo "Listing inventory hosts..."
                    ansible-inventory -i ${INVENTORY_FILE} --list
                    
                    echo "Testing connectivity to hosts..."
                    ansible -i ${INVENTORY_FILE} all -m ping -v || true
                '''
            }
        }
        
        stage('Dry Run - lab5') {
            steps {
                echo '========== Running lab5 playbook in check mode =========='
                sh '''
                    ansible-playbook -i ${INVENTORY_FILE} ${PLAYBOOK_LAB5} --check -v
                '''
            }
        }
        
        stage('Dry Run - lab6') {
            steps {
                echo '========== Running lab6 playbook in check mode =========='
                sh '''
                    ansible-playbook -i ${INVENTORY_FILE} ${PLAYBOOK_LAB6} --check -v
                '''
            }
        }
        
        stage('Execute lab5 Playbook') {
            when {
                branch 'main'
            }
            steps {
                echo '========== Executing lab5 playbook =========='
                sh '''
                    ansible-playbook -i ${INVENTORY_FILE} ${PLAYBOOK_LAB5} \
                        --vault-password-file <(echo $ANSIBLE_VAULT_ID) \
                        -v
                '''
            }
        }
        
        stage('Execute lab6 Playbook') {
            when {
                branch 'main'
            }
            steps {
                echo '========== Executing lab6 playbook =========='
                sh '''
                    ansible-playbook -i ${INVENTORY_FILE} ${PLAYBOOK_LAB6} \
                        --vault-password-file <(echo $ANSIBLE_VAULT_ID) \
                        -v
                '''
            }
        }
    }
    
    post {
        always {
            echo '========== Pipeline Execution Summary =========='
            cleanWs()
        }
        success {
            echo '✓ Ansible playbooks executed successfully!'
            // Add email notification or webhook here if needed
        }
        failure {
            echo '✗ Pipeline failed! Check logs for details.'
            // Add email notification or webhook here if needed
        }
    }
}
