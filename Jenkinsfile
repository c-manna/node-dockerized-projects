pipeline {
    agent {
        docker {
            image 'node:16'  // Use the Node.js Docker image which includes npm
            args '-u node'
        }
    }
    stages{
        stage("checkout"){
            steps{
                checkout scm
            }
        }

        stage("Test"){
            steps{
                // Run npm install and test inside the Docker container
                sh 'npm install'
                sh 'npm test'
            }
        }

        stage("Build"){
            steps{
                // Run the build inside the Docker container
                sh 'npm run build'
            }
        }

        stage("Build Image"){
            steps{
                // Run Docker build inside the container (Docker-in-Docker)
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
