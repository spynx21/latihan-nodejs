// pipeline{
//     agent none
//     environment{
//         docker = credentials ('dockerlogin')
//     }
//     stages{
//         stage ('build aplication'){
//             agent{
//                 docker{
//                     image 'node:lts-buster-slim'
//                 }
//             }
//             steps{
//                 sh 'npm install'
//             }
//         }
//         stage ('build docker image and push to dockerhub'){
//             agent{
//                 docker{
//                     image 'docker:dind'
//                     args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
//                 }
//             }
//             steps{
//                 sh 'docker build -t risvan21/apknode:0.1 .'
//                 sh 'echo $docker_PSW | docker login -u $docker_USR --password-stdin'
//                 sh 'docker push risvan21/apknoded:0.1'
//             }
//         }
//         stage ('deploy aplication'){
//             agent {
//                 docker{
//                     image 'kroniak/ssh-client'
//                     args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
//                 }
//             }
//             steps{
//                 withCredentials([sshUserPrivateKey(credentialsId: "ssh", keyFileVariable:"keyfile")]){
//                     sh 'ssh -i ${keyfile} -o StrictHostKeyChecking=no ipan@172.20.10.3 "echo $docker_PSW | docker login -u $docker_USR --password-stdin"'
//                     sh 'ssh -i ${keyfile} -o StrictHostKeyChecking=no ipan@172.20.10.3 docker pull risvan21/apknode:0.1'
//                     sh 'ssh -i ${keyfile} -o StrictHostKeyChecking=no ipan@172.20.10.3 docker rm --force mongodb'
//                     sh 'ssh -i ${keyfile} -o StrictHostKeyChecking=no ipan@172.20.10.3 docker run --detach --name mongo -p 27017:27017 mongo:3'
//                     sh 'ssh -i ${keyfile} -o StrictHostKeyChecking=no ipan@172.20.10.3 docker rm --force nodejsgoof'
//                     sh 'ssh -i ${keyfile} -o StrictHostKeyChecking=no ipan@172.20.10.3 docker run -it --detach --name nodejsguf --network host risvan21/apknode:0.1'
//                 } 
//             }
//         }
//     }
// }

pipeline {
    agent none
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerlogin')
    }
    stages {
        stage('build application') {
            agent {
                docker {
                    image 'node:lts-buster-slim'
                }
            }
            steps {
                sh 'npm install'
            }
        }
        stage('build docker image and push to dockerhub') {
            agent {
                docker {
                    image 'docker:dind'
                    args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                sh 'docker build -t risvan21/apknode:0.1 .'
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push risvan21/apknode:0.1'
            }
        }
        stage('deploy application') {
            agent {
                docker {
                    image 'kroniak/ssh-client'
                    args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'ssh', keyFileVariable: 'keyfile')]) {
                    sh """ssh -i ${keyfile} -o StrictHostKeyChecking=no ipan@172.20.10.3 "echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin"""
                    sh """ssh -i ${keyfile} -o StrictHostKeyChecking=no ipan@172.20.10.3 docker pull risvan21/apknode:0.1"""
                    sh """ssh -i ${keyfile} -o StrictHostKeyChecking=no ipan@172.20.10.3 docker rm --force mongodb"""
                    sh """ssh -i ${keyfile} -o StrictHostKeyChecking=no ipan@172.20.10.3 docker run --detach --name mongo -p 27017:27017 mongo:3"""
                    sh """ssh -i ${keyfile} -o StrictHostKeyChecking=no ipan@172.20.10.3 docker rm --force nodejsgoof"""
                    sh """ssh -i ${keyfile} -o StrictHostKeyChecking=no ipan@172.20.10.3 docker run -it --detach --name nodejsgoof --network host risvan21/apknode:0.1"""
                }
            }
        }
    }
}
