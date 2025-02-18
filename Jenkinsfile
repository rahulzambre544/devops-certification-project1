pipeline {
  agent any
  tools {
    maven 'Maven'
  }
  stages {
    stage ('GetCode from SCM'){
      steps{
         git 'https://github.com/shaik-haneef001/devops-certification-project1.git'
      }
    }
    stage ("terraform init") {
      steps{
         sh ('terraform init -reconfigure') 
      }
    }
    stage ("terraform plan") {
      steps{
         sh ('terraform plan') 
      }
    }            
    stage ("terraform apply") {
      steps{
         echo "Terraform action"
         sh ('terraform apply')
      }
    }
    stage ('Build the package'){
      steps{
         sh 'mvn clean package'
      }
    }
    stage('Building Docker image') {
      steps{
        script {
          dockerImage = docker.build("haneef7/Project:V1")
        }
      }
    }
    stage('Push Docker Image to Dockerhub') {
      steps{
        script {
            withDockerRegistry([ credentialsId: "dockerhub", url: "" ]){
            dockerImage.push()
            }
        }
      }
    }
    stage(" Deploying app onto Test_server using Ansible") {
        steps {
            ansiblePlaybook credentialsId: 'Ansible-deploy', disableHostKeyChecking: true, installation: 'Ansible', inventory: 'stage.inv', playbook: 'Docker-deploy-1.yml'
        }    
    }
    stage(" Deploying app onto Prod_server using Ansible") {
        steps {
            ansiblePlaybook credentialsId: 'Ansible-deploy', disableHostKeyChecking: true, installation: 'Ansible', inventory: 'prod.inv', playbook: 'Docker-deploy-2.yml'
        }    
    }
    stage(" Email-Notify"){
        steps {
            emailext attachLog: true, body: '''Hello,
            Check your build status and build logs.
            Thankyou.''', recipientProviders: [developers()], subject: 'Build Success or Failure?', to: 'khan.sharukh112@gmail.com'
        }
    }
  }
}