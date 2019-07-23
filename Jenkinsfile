pipeline {
  agent any
  tools { 
        maven 'Maven'
 }
  stages {
    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace... */
      steps {
        checkout scm
      }
    }
    stage('Build') {
      steps {
        sh 'mvn -B -DskipTests clean package'
        sh 'echo $USER'
        sh 'echo whoami'
      }
    }
    stage('Push image to private docker-hub') {
      steps {
        withDockerRegistry([credentialsId: 'docker-hub', url: "https://index.docker.io/v1/"]) {
          sh 'sudo docker login https://index.docker.io/v1/ -u=anil9848 -p=Password@12345'
          sh 'sudo /usr/bin/docker push anil9848/account-service:latest'
        }
      }
    }
    stage('Push image to aws ecr'){
      steps {
       withDockerRegistry(credentialsId: 'ecr:us-east-1:aws-credentials', url: 'http://651445660648.dkr.ecr.us-east-1.amazonaws.com/sample') {
       sh 'docker tag anil9848/account-service:latest 651445660648.dkr.ecr.us-east-1.amazonaws.com/sample'
         sh 'docker push 651445660648.dkr.ecr.us-east-1.amazonaws.com/sample'
}
      }
    }
    stage('Run docker image on kubernetes cluster') {
      steps {
        node('EKS-master'){
          checkout scm
         sh 'kubectl apply -f deployment.yaml'
         sh 'kubectl apply -f service.yaml'
        }
      }
    }
  }
}
