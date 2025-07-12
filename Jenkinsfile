pipeline{
    agent none
    environment{
        docker = credentials ('dockerlogin')
    }
    stages{
        stage ('build aplication'){
            agent{
                docker{
                    image 'node:lts-buster-slim'
                }
            }
            steps{
                sh 'npm install'
            }
        }
        stage ('build docker image and push to dockerhub'){
            agent{
                docker{
                    image 'docker:dind'
                    args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps{
                sh 'docker build -t risvan21/apknoded:0.1 .'
                sh 'echo $docker_PSW | docker login -u $docker_USR --password-stdin'
                sh 'docker push risvan21/apknoded:0.1'
            }
        }
        stage ('deploy aplication'){
            agent {
                docker{
                    image 'kroniak/ssh-client'
                    args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps{
                withCredentials([sshUserPrivateKey(credentialsId: "ssh", KeyFileVariable:"keyfile")]){
                    sh 'ssh ipan@172.20.10.2 -i ${keyfile} -o StrictHostKeyChecking=no "echo $docker_PSW | docker login -u $docker_USR --password-stdin"'
                    sh 'ssh ipan@172.20.10.2 -i ${keyfile} -o StrictHostKeyChecking=no docker pull image risvan21/apknode:0.1'
                    sh 'ssh ipan@172.20.10.2 -i ${keyfile} -o StrictHostKeyChecking=no docker rm --force mongodb'
                    sh 'ssh ipan@172.20.10.2 -i ${keyfile} -o StrictHostKeyChecking=no docker run --detach --name mongo -p 27017:27017 mongo:3'
                    sh 'ssh ipan@172.20.10.2 -i ${keyfile} -o StrickHostKeyChecking=no docker rm --force nodejsgoof'
                    sh 'ssh ipan@172.20.10.2 -i ${keyfile} -o StrictHostKeyChecking=no docker run -it --detach --name nodejsguf --network host risvan21/apknode:0.1'
                }
            }
        }
    }
}