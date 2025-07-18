pipeline {
    agent {label 'K-M'}

    environment {
        DOCKER_IMAGE = "nashit836/taskimage:latest"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git url: 'https://github.com/cloudnash/Website-PRT-ORG.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh '''
                        echo "$PASSWORD" | docker login -u "$USERNAME" --password-stdin
                        docker push $DOCKER_IMAGE
                    '''
                    sh 'sudo docker build /home/ubuntu/jenkins/workspace/job-pipeline/ -t nashit836/taskimage:latest '
                    sh 'sudo docker push nashit836/taskimage:latest '
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'
            }
        }
    }

    triggers {
        githubPush()  // Triggers the pipeline on GitHub push event
    }
}
