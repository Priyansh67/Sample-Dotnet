pipeline {
    agent any

    environment {
        IMAGE_NAME = 'priyansh67/sampledotnet'
        IMAGE_TAG = 'latest'
        CONTAINER_NAME = 'sampledotnetapp'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'dotnet build src/SampleDotnet/SampleDotnet.csproj --configuration Release'
            }
        }

        stage('Test') {
            steps {
                sh 'dotnet test src/SampleDotnet/SampleDotnet.sln --logger "trx;LogFileName=./SampleDotnet.trx"'
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }

        stage('Docker Push') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker_login') {
                        docker.image("${IMAGE_NAME}:${IMAGE_TAG}").push()
                    }
                }
            }
        }

        stage('Run Container') {
            steps {
                script {
                    sh 'docker rm -f ${CONTAINER_NAME} || true'
                    sh "docker run -d --name ${CONTAINER_NAME} -p 3000:80 ${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }
    }
}