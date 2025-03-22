pipeline {
    agent {
        docker {
            image 'node:16'  // Use Node.js Docker image for Test and Build stages
            args '-v /var/run/docker.sock:/var/run/docker.sock'  // Mount Docker socket to container
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
                // Install sudo inside the Docker container
                sh 'apt-get update && apt-get install -y sudo'

                // Now use sudo to fix the ownership
                sh 'sudo chown -R $(whoami):$(whoami) /.npm'
                
                // Run npm install and test inside the Docker container
                sh 'npm install'
                sh 'npm test'
            }
        }

        stage("Build") {
            steps {
                // Run npm build inside the Docker container
                sh 'npm run build'
            }
        }

        stage("Build Image") {
            agent {
                // Run Docker build on the Jenkins host (outside the container)
                label 'docker-host'
            }
            steps {
                // Run Docker build on the Jenkins host (outside the container)
                sh 'docker build -t my-node-app:1.0 .'
            }
        }

        stage('Docker Push') {
            agent {
                // Run Docker push on the Jenkins host (outside the container)
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
