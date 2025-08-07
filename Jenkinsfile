pipeline {
    agent {
        docker {
            image 'docker:latest'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
        KUBECONFIG_CREDENTIALS = credentials('kubeconfig')
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    stages {
        stage('Checkout') {
            steps {
                git(
                    url: 'https://github.com/orvencasido/microservices.git',
                    branch: 'main',
                    credentialsId: 'github-creds'
                )
            }
        }
        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'docker build -t orvencasido/frontend:${IMAGE_TAG} .'
                }
            }
        }
        stage('Build API') {
            steps {
                dir('api') {
                    sh 'docker build -t orvencasido/api:${IMAGE_TAG} .'
                }
            }
        }
        stage('Push to DockerHub') {
            steps {
                withDockerRegistry(credentialsId: 'dockerhub-creds') {
                    sh 'docker push orvencasido/frontend:${IMAGE_TAG}'
                    sh 'docker push orvencasido/api:${IMAGE_TAG}'
                }
            }
        }
        stage('Deploy to K8s via Helm') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    sh '''
                        export KUBECONFIG=$KUBECONFIG
                        helm upgrade --install microservice-app ./helm-chart \
                          --set frontend.image.tag=${IMAGE_TAG} \
                          --set api.image.tag=${IMAGE_TAG}
                    '''
                }
            }
        }
    }
}
