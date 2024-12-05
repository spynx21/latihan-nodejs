pipeline {
    agent none
    environment {
        DOCKERHUB_CREDENTIALS = credentials('DockerLogin')
    }
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:lts-buster-slim'
                }
            }
            steps {
                sh 'npm install'
            }
        }
        stage( 'Build Docker Image and Push to Docker Registry') {
            agent {
                docker {
                    image 'docker:dind'
                    args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                sh 'docker build -t risvan21/aplikasijsgoof:0.1 .'
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR -password-stdin'
                sh 'docker push risvan21/aplikasijsgoof:0.1'
            }
        }
        stage('Deploy Docker Image'){
            agent {
                docker {
                    image 'kroniak/ssh-client'
                    args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: "sshkey", KeyFileVariable: 'keyfile')]) {
                    sh 'ssh -i ${keyfile} -o StrictHostKeyChecking=no zaet@172.20.10.2 "echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR -password-stdin"'
                    sh 'ssh -i ${keyfile} -o StrictHostKeyChecking=no zaet@172.20.10.2 docker pull risvan21//aplikasijsgoof:0.1'
                    sh 'ssh -i ${keyfile} -o StrictHostKeyChecking=no zaet@172.20.10.2 docker rm --force mongodb'
                    sh 'ssh -i ${keyfile} -o StrictHostKeyChecking=no zaet@172.20.10.2 docker run --detach --name mongodb -p 27017:27017 mongo:3'
                    sh 'ssh -i ${keyfile} -o StrictHostKeyChecking=no zaet@172.20.10.2 docker rm --force nodejsgoof'
                    sh 'ssh -i ${keyfile} -o StrictHostKeyChecking=no zaet@172.20.10.2 docker run -it --detach --name nodejsgoof --network host risvan21/aplikasijsgoof:0.1'

                }
            }
        }
    }
}