pipeline {
    agent none  // Define agent at the stage level

    stages {
        stage("Checkout") {
            agent any  // Use any available agent to check out the source code
            steps {
                checkout scm
            }
        }

        stage("Test") {
            agent {
                docker {
                    image 'node:16'
                    args '-v /var/run/docker.sock:/var/run/docker.sock -u root'
                }
            }
            steps {
                sh 'apt-get update && apt-get install -y sudo'
                sh 'chown -R $(whoami):$(whoami) /root/.npm'
                sh 'npm install'
                sh 'npm test'
            }
        }

        stage("Build") {
            agent {
                docker {
                    image 'node:16'
                    args '-v /var/run/docker.sock:/var/run/docker.sock -u root'
                }
            }
            steps {
                sh 'npm run build'
            }
        }

        stage("Build Image") {
            agent {
                label 'docker-host'
            }
            steps {
                sh 'docker build -t my-node-app:1.0 .'
            }
        }

        stage('Docker Push') {
            agent {
                label 'docker-host'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker_cred', passwordVariable: 'DOCKERHUB_PASSWORD', usernameVariable: 'DOCKERHUB_USERNAME')]) {
                    sh 'docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD'
                    sh 'docker tag my-node-app:1.0 chandrakantamanna/my-node-app:1.0'
                    sh 'docker push chandrakantamanna/my-node-app:1.0'
                    sh 'docker logout'
                }
            }
        }
    }
}
