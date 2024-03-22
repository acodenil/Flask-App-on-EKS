pipeline {
    agent any
    
    environment {
        MANIFESTS_FOLDER = '/Flask-App-on-EKS/eks-manifests'
    }
    stages{
        stage("Code"){
            steps{
                git url: "https://github.com/acodenil/Flask-App-on-EKS.git", branch: "main"
            }
        }
        stage("Build & Test"){
            steps{
                sh "docker build . -t flaskapp"
            }
        }
        stage("Push to DockerHub"){
            steps{
                withCredentials([usernamePassword(credentialsId:"dockerHub",passwordVariable:"dockerHubPass",usernameVariable:"dockerHubUser")]){
                    sh "echo ${env.dockerHubPass} | docker login -u ${env.dockerHubUser} --password-stdin"
                    sh "docker tag flaskapp ${env.dockerHubUser}/flaskapp:latest"
                    sh "docker push ${env.dockerHubUser}/flaskapp:latest" 
                }
            }
        }
        stage('Deploying Manifests') {
            steps {
                sh 'kubectl apply -f $MANIFESTS_FOLDER/mysql-secrets.yml -f $MANIFESTS_FOLDER/mysql-configmap.yml -f $MANIFESTS_FOLDER/mysql-deployment.yml -f $MANIFESTS_FOLDER/mysql-svc.yml'
                sh 'kubectl apply -f $MANIFESTS_FOLDER/two-tier-app-deployment.yml -f $MANIFESTS_FOLDER/two-tier-app-svc.yml'
            }
        }
    }
    
    post {
        success {
            echo 'Deployment successful'
        }
        failure {
            echo 'Deployment failed'
        }
    }
}
