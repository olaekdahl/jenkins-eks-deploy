pipeline {
    agent any
    options {
        skipStagesAfterUnstable()
    }
    stages {
        // stage('Clone repository') { 
        //     steps { 
        //         script{
        //         checkout scm
        //         }
        //     }
        // }

        stage('Build') { 
            steps { 
                script{
                 app = docker.build("octopus-underwater-app")
                }
            }
        }
        stage('Test'){
            steps {
                 echo 'Empty'
            }
        }
        stage('Push') {
            steps {
                script{
                    docker.withRegistry('https://397188165174.dkr.ecr.us-west-1.amazonaws.com/octopus-underwater-app', 'ecr:us-west-1:aws-credentials') {
                    app.push("${env.BUILD_NUMBER}")
                    app.push("latest")
                    }
                }
            }
        }
        stage('Deploy'){
            steps {
                sh 'kubectl config use-context ola-admin@jenkins-cluster.us-west-1.eksctl.io'
                sh 'kubectl apply -f deployment.yml'
            }
        }

    }
}