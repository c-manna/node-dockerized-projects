pipeline {
    agent any  // Use the default Jenkins agent

    stages {
        stage("Checkout") {
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
            steps {
                sh 'docker build -t my-node-app:1.0 .'
            }
        }

        stage('Docker Push') {
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
