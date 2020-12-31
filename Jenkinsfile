pipeline {
    agent any

    triggers {
        pollSCM '* * * * *'
    }
    stages {
        stage('Pull') {
            steps {
                sh 'ssh -o StrictHostKeyChecking=no root@135.181.97.45 "cd /home/api-gateway && git pull"'
            }
        }
        stage('Build App') {
            steps {
                sh 'ssh -o StrictHostKeyChecking=no root@135.181.97.45 "cd /home/api-gateway && ./gradlew assemble"'
            }
        }
        stage('Build Image') {
            steps {
                sh 'ssh -o StrictHostKeyChecking=no root@135.181.97.45 "cd /home/api-gateway && docker build -f Dockerfile --tag=api-gateway --force-rm=true ."'
            }
        }
        stage('Clean old container') {
            steps {
                sh 'ssh -o StrictHostKeyChecking=no root@135.181.97.45 "docker stop api-gateway && docker rm -vf api-gateway"'
            }
        }
        stage('Run') {
            steps {
                sh 'ssh -o StrictHostKeyChecking=no root@135.181.97.45 "cd /home/api-gateway && docker run -d --name api-gateway -p 8011:8011 -e "spring.rabbitmq.host=135.181.97.45" api-gateway:latest"'
            }
        }
    }
}
