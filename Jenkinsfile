pipeline {
    agent any

    environment {
        IMAGE_NAME = "aws-flask-app"
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/sergey-konovalenko/aws-flask-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build(IMAGE_NAME)
                }
            }
        }

        stage('Deploy to Remote EC2') {
            steps {
                script {
                    sshagent(credentials: ['ec2-ssh-key']) {
                        sh '''
                            docker save simple-flask-app | bzip2 | ssh -o StrictHostKeyChecking=no ec2-user@<target-ec2-ip> 'bunzip2 | docker load'
                            ssh -o StrictHostKeyChecking=no ec2-user@<target-ec2-ip> '
                              docker stop flask || true &&
                              docker rm flask || true &&
                              docker run -d -p 80:5000 --name flask simple-flask-app
                            '
                        '''
                    }
                }
            }
        }
    }
}
