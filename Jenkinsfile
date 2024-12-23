pipeline {
    agent any
    environment {
        SERVER_IP = credentials("prod-server-ip")

    }
    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/Abhi0847/jenkins-project.git', branch: 'main'
                sh "ls -ltr"
            }
        }
        stage('Setup') {
            steps {
                sh "pip install -r requirements.txt"
            }
        }
        stage('Test') {
            steps {
                sh "pytest"
                sh "whoami"
            }
        }
        stage('Package Code') {
            steps {
                sh "zip -r myapp.zip ./* -x '*.git*' "
                sh "ls -lrt"
            }
        }
        stage('Deploy') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'ssh-key', keyFileVariable: 'MY_SSH_KEY', usernameVariable: 'username')]) {
                sh '''
                scp -i $MY_SSH_KEY -o StrictHostKeyChecking=no myapp.zip ${username}@${SERVER_IP}:/home/ec2-user/
                ssh -i $MY_SSH_KEY -o StrictHostKeyChecking=no ${username}@${SERVER_IP} << EOF
                    unzip -o /home/ec2-user/myapp.zip -d /home/ec2-user/app
                    cd /home/ec2-user
                    source app/venv/bin/activate
                    cd /home/ec2-user/app/
                    pip install -r requirements.txt
                    sudo systemctl restart flaskapp.service  
EOF
                    
                '''
                }
            }
        }
    }
}