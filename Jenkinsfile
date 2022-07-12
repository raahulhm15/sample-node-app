@Library('github.com/releaseworks/jenkinslib') _

pipeline {
    agent any
    environment {
        registry = "${awsid}.dkr.ecr.us-east-1.amazonaws.com/finalprojectecr:latest"
    }

    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/raahulhm15/sample-app.git']]])
            }
        }
    // Building Docker images
    stage('Building image') {
      steps{
        script {

          dockerImage = docker.build registry
        }
      }
    }

    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
        steps{
            script {
                sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 334982178958.dkr.ecr.us-east-1.amazonaws.com/finalprojectecr:latest '
                sh 'docker push ${awsid}.dkr.ecr.us-east-1.amazonaws.com/finalprojectecr:latest'
            }
        }
    }

    stage('Docker Deploy') {
     steps{
         script {
             sshagent(credentials : ['upgrad']){
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@10.0.2.230 "docker login -u AWS -p $(aws ecr get-login-password --region us-east-1) ${awsid}.dkr.ecr.us-east-1.amazonaws.com/finalprojectecr:latest && docker pull ${awsid}.dkr.ecr.us-east-1.amazonaws.com/finalprojectecr:latest && (docker ps -f name=node -q | xargs --no-run-if-empty docker container stop) && (docker container ls -a -fname=node -q | xargs -r docker container rm) && docker run -d -p 8081:8081 --rm --name node ${awsid}.dkr.ecr.us-east-1.amazonaws.com/finalprojectecr:latest"'

             }


            }
      }
    }
    }
}
