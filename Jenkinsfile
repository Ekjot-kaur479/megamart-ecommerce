pipeline {
    agent any

    environment {
        
        GIT_REPO = 'git@github.com:Ekjot-kaur479/megamart-ecommerce.git'
        BRANCH = 'main'
        EC2_IP = '3.82.20.165'
        EC2_USER = 'ubuntu'   // change if your EC2 AMI uses ec2-user/other
        APACHE_DIR = '/var/www/html'
        SSH_KEY = 'new-one-key'  // Jenkins SSH key credential
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: "${BRANCH}",
                    url: "${env.GIT_REPO}",
                    credentialsId: "${env.GIT_CREDENTIALS}"
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'sudo apt-get update -y'
                sh 'sudo apt-get install -y nodejs npm'
                sh 'npm install'
            }
        }

        stage('Build React App') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Deploy to EC2 Apache') {
            steps {
                sshagent (credentials: ['new-one-key']) {
                    sh """
                        scp -o StrictHostKeyChecking=no -r build/* ${EC2_USER}@${EC2_IP}:${APACHE_DIR}/
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful! Visit http://${EC2_IP}"
        }
        failure {
            echo "❌ Deployment failed. Check Jenkins logs."
        }
    }
}
