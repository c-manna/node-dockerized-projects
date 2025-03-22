pipeline {
    agent {
        docker {
            image 'node:16'  // Use Node.js Docker image
            args '-u root'   // Run as root for installation
        }
    }
    stages {
        stage("checkout") {
            steps {
                checkout scm
            }
        }

        stage("Test") {
            steps {
                // Fix ownership of the correct .npm directory (user's home directory)
                sh 'chown -R $(whoami):$(whoami) ~/.npm'

                // Run npm install and test inside the Docker container
                sh 'npm install'
                sh 'npm test'
            }
        }

        stage("Build") {
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
