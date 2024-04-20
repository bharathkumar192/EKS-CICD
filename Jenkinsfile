pipeline {
    agent any
    environment {
        DOCKERHUB_USERNAME="bharathkumar192"
        APP_NAME="django-arco-app"
        IMAGE_TAG="${BUILD_NUMBER}"
        IMAGE_NAME="${DOCKERHUB_USERNAME}"+"/"+"${APP_NAME}"
        REGISTRY_CREDS='dockerhub-token'
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
                    git creadentialsId : 'github_direct',
                    url: 'https://github.com/bharathkumar192/EKS-CICD.git',
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
            steps {
                script {
                    sh """
                    cat deployment.yml
                    sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yml
                    cat deployment.yml
                    mkdir -p ~/.ssh
                    ssh-keyscan github.com >> ~/.ssh/known_hosts
                    """
                }
            }
        }

        stage('Push the changed deployment file to git') {
        steps {
            sshagent(credentials: ['githubUser']) { // Assuming 'githubUser' is the ID of your SSH credential 
                sh """
                    git config --global user.name "bharathkumar192"
                    git config --global user.email "bharathkumar1922001@gmail.com"
                    git add deployment.yml
                    git commit -m "Update deployment file"
                    git push git@github.com:bharathkumar192/EKS-CICD.git master 
                """
             }
         }
     }

        stage('Deploy image to AWS EKS') {
            steps {
                withAWS(credentials: 'aws-credentials', region: 'eu-north-1') {
                    sh 'echo "STAGE 4: Deploying image to AWS EKS cluster ..."'
                    script { // You need to wrap your try block inside a script block
                        try {
                            sh 'aws eks update-kubeconfig --name JenkinsApp'
                            sh 'kubectl config use-context arn:aws:eks:eu-north-1:730335486616:cluster/JenkinsApp'
                            sh 'kubectl apply -f deployment.yml'
                            sh 'kubectl apply -f service.yml'
                            sh 'echo "Congratulations! Deployment successful."'
                        } catch (Exception e) {
                            sh 'echo "Failed to deploy to Kubernetes. Error: $e"'
                            error "Stopping the build due to failure in deployment."    
                        }
                    }
                }
            }
        }
    }
    
}
