pipeline {
    agent {
        docker {
            image 'node:16'  // Use Node.js Docker image for the Test and Build stages
            args '-u root'   // Ensure we run with root privileges (if needed)
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
                // Install dependencies and run tests inside the Docker container
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
            agent {
                // Run Docker build outside the Docker container (on the host)
                docker {
                    image 'docker:latest'  // Use Docker CLI in the Jenkins host
                    args '-u root'
                }
            }
            steps{
                // Run Docker build on the Jenkins host
                sh 'docker build -t my-node-app:1.0 .'
            }
        }

        stage('Docker Push') {
            agent {
                docker {
                    image 'docker:latest'  // Use Docker CLI for Docker Push on the Jenkins host
                    args '-u root'
                }
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker_cred', passwordVariable: 'DOCKERHUB_PASSWORD', usernameVariable: 'DOCKERHUB_USERNAME')]) {
                    // Docker login, push the image, and logout on the host machine
                    sh 'docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD'
                    sh 'docker tag my-node-app:1.0 chandrakantamanna/my-node-app:1.0'
                    sh 'docker push chandrakantamanna/my-node-app:1.0'
                    sh 'docker logout'
                }
            }
        }
    }
}
