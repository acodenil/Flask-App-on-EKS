pipeline {
    agent any
    
    environment {
        MANIFESTS_FOLDER = '/Flask-App-on-EKS/eks-manifests'
        AWS_ACCESS_KEY_ID = credentials('AKIA2BSJGGHJZY4QWQWI')
        AWS_SECRET_ACCESS_KEY = credentials('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')
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
    
}
