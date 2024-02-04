pipeline {
    agent any
    environment {
        DOCKERHUB_USERNAME="clinkratcheat"
        APP_NAME="gitops-arco-app"
        IMAGE_TAG="${BUILD_NUMBER}"
        IMAGE_NAME="${DOCKERHUB_USERNAME}"+"/"+"${APP_NAME}"
        REGISTRY_CREDS='dockerhub'
    }
    stages { 
        stage('Cleanup workspace') {
            steps {
                script{
                    cleanWs()
                 }
            }
        }
        stage('Checkout SCM'){
            steps{
                script{
                    git creadentialsId : 'github',
                    url: 'https://github.com/jeet004a/EKS-CICD',
                    branch: 'master'
                }
            }
        }
        stage('Build Docker Image'){
            steps{
                script{
                    docker_image= docker.build "${IMAGE_NAME}"
                }
            }
        }
        stage('Push Docker Image'){
            steps{
                script{
                    docker.withRegistry('',REGISTRY_CREDS){
                        docker_image.push("$BUILD_NUMBER")
                        docker_image.push('latest')
                    }
                }
            }
        }
        stage('Delete Docker images'){
            steps{
                script{
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
                }
            }
        }
        stage('Updating k8s deployment file'){
            steps{
                script{
                    sh """
                        cat deployment.yml
                        sed -i 's/${APP_NAME}.*/${APP_NAME}/g' deployment.yml
                        cat deployment.yml
                    """
                }
            }
        }
    }
    
}

//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