
pipeline {
    agent any 

    environment {
        DOCKER_CREDENTIALS_ID = 'roseaw-dockerhub'                                 // <------DON'T change this
        DOCKER_IMAGE = 'cithit/shwanaf'                                                 // <------change this
        IMAGE_TAG = "build-${BUILD_NUMBER}"
        GITHUB_URL = 'https://github.com/Farha-Shwana/225-lab3-1.git'                   // <------change this
        KUBECONFIG = credentials('shwanaf-255')                                             // <------change this
    } 

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']],
                          userRemoteConfigs: [[url: "${GITHUB_URL}"]]])
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${IMAGE_TAG}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKER_CREDENTIALS_ID}") {
                        docker.image("${DOCKER_IMAGE}:${IMAGE_TAG}").push()
                    }
                }
            }
        }

        stage('Deploy to Dev Environment using NodePort') {
            steps {
                script {
                    // Set up Kubernetes configuration using the specified KUBECONFIG
                    def kubeConfig = readFile(KUBECONFIG)
                    // Update deployment-dev.yaml to use the new image tag
                    // sh "sed -i 's|${DOCKER_IMAGE}:latest|${DOCKER_IMAGE}:${IMAGE_TAG}|' deployment-dev.yaml
                    sh "sed -i 's|cithit/shwanaf:latest|cithit/shwanaf:${IMAGE_TAG}|' deployment-dev.yaml"
                    // sh "kubectl apply -f deployment-dev.yaml"
                    sh "kubectl --kubeconfig=/var/lib/jenkins/kubeconfigs/shwanaf-225 apply -f deployment-dev.yaml"
                }
            }
        }
 
        stage('Check Kubernetes Cluster') {
            steps {
                script {
                   // sh "kubectl get all"
                    sh "kubectl --kubeconfig=/var/lib/jenkins/kubeconfigs/shwanaf-225 get all"
                }
            }
        }
    }
}
