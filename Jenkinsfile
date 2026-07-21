pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/shivashankar44/ansible_new.git'
            }
        }
        
        stage('Install Ansible') {
            steps {
                sh 'sudo dnf -y install ansible-core'
            }
        }
        
        stage('Run Ansible Playbook') {
            steps {
                sh 'ansible-playbook -i iniventory.ini lab5.yaml -v'
            }
        }
    }
}
