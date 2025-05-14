pipeline {
    agent any

    environment {
        NODE_ENV = 'production'
        EC2_USER = 'ubuntu'
        EC2_IP = '3.226.209.26'
        REMOTE_PATH = '/home/ubuntu/JenkisClass'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'QA', url: 'https://github.com/Norhan15/JenkisClass.git'
            }
        }

        stage('Build') {
            steps {
                sh 'rm -rf node_modules'
                sh 'npm ci'
            }
        }

        stage('Deploy') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'ssh-key-ec2', keyFileVariable: 'KEY')]) {
                    sh '''
                        ssh -i "$KEY" -o StrictHostKeyChecking=no $EC2_USER@$EC2_IP '
                            cd $REMOTE_PATH &&
                            git pull origin QA &&
                            npm ci &&
                            pm2 restart health-api || pm2 start server.js --name health-api
                        '
                    '''
                }
            }
        }
    }
}
