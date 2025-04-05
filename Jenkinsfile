pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'java-web-app-cicd:latest'
        CONTAINER_NAME = 'java-web-app-cicd'
        DOCKER_USERNAME = 'vinayz7'
        DOCKER_PASSWORD = 'dckr_pat_Nd_U85FWpyzTBJxZX7U55noic2o'
        DOCKER_REPO = 'vinayz7/java-web-app-cicd:latest'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/ustdevops/cicd.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Remove Existing Container') {
            steps {
                script {
                    def containerExists = sh(script: "docker ps -a | grep '$CONTAINER_NAME'", returnStatus: true) == 0
                    if (containerExists) {
                        sh "docker rm -f $CONTAINER_NAME"
                    }
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                sh 'docker run -d -p 8882:8080 --name $CONTAINER_NAME $DOCKER_IMAGE'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Docker Push') {
            steps {
                sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                sh 'docker commit $CONTAINER_NAME $DOCKER_REPO'
                sh 'docker push $DOCKER_REPO'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl delete deploy --all'
                sh 'kubectl apply -f deploy-tomcat.yaml'
            }
        }
    }
}


