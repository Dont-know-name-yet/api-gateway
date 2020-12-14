pipeline {
    agent any

    triggers {
        pollSCM '* * * * *'
    }
    stages {
        stage('Build') {
            steps {
                sh './gradlew assemble'
            }
        }
        stage('Build Docker image') {
            steps {
                sh './gradlew docker'
            }
        }
        stage('Push Docker image') {
            environment {
                DOCKER_HUB_LOGIN = credentials('docker-hub')
            }
            steps {
                sh 'docker login --username=$DOCKER_HUB_LOGIN_USR --password=$DOCKER_HUB_LOGIN_PSW'
                sh './gradlew dockerPush'
            }
        }
        stage('Deploy on Dev Server') {
            environment {
                deletePreviousImages = 'docker rmi -f $(docker images -a -q)'
                deletePreviousContainers = 'docker rm -vf $(docker ps -a -q)'
                dockerRun = 'docker run -p 8080:8080 -d --name my-app tomislav10/myproject-spring-boot:1.0-SNAPSHOT'
            }
            steps {
                sshagent(['backend-server']) {
                    sh 'ssh -o StrictHostKeyChecking=no root@167.99.145.37 "${deletePreviousContainers} && ${deletePreviousImages} && ${dockerRun}"'
                }
            }
        }

    }
}
