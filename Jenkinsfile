pipeline {
    agent any
    stages{
        stage("checkout"){
            steps{
                checkout scm
            }
        }

        stage("Install Node.js and npm") {
            steps {
                // Install the latest version of Node.js and npm as root
                sh '''
                # Update and install curl
                sudo apt-get update
                sudo apt-get install -y curl
                
                # Install Node.js (latest stable version) from NodeSource
                curl -fsSL https://deb.nodesource.com/setup_current.x | sudo -E bash -
                sudo apt-get install -y nodejs
                
                # Ensure the latest npm version is installed
                sudo npm install -g npm@latest
                '''
            }
        }

        stage("Test") {
            steps {
                sh 'npm test'
            }
        }

        stage("Build"){
            steps{
                sh 'npm run build'
            }
        }

        stage("Build Image"){
            steps{
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
