pipeline {
    agent any

    enviroment{
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
        },

        stage('Checkout SCM'){
            steps{
                script{
                    git creadentialsId : 'github',
                    url: 'https://github.com/jeet004a/EKS-CICD',
                    branch: 'master'
                }
            }
        }
    }
    
}

//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