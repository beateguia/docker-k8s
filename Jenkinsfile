pipeline {
    agent any
   
    environment {
      DOCKERHUB_CREDENTIALS = credentials('dockerhubcredentials')
    }
  stages {
    stage('Cloning our Git') {
        steps {
          git credentialsId: 'mygithubcredentials', url: 'https://github.com/beateguia/docker-k8s.git', branch: 'main'
        }
    }
    stage('Docker Build and Tag') {
      steps {
          sh 'docker build -t docker-k8s:latest .'
          sh 'docker tag docker-k8s bteguia/sqacontainer:latest'
          sh 'docker tag docker-k8s bteguia/sqacontainer:$BUILD_NUMBER'
      }
    }

    stage('Publish image to Docker Hub') {
      steps {
          sh  'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin '
          sh  'docker push bteguia/sqacontainer:latest'
          sh  'docker push bteguia/sqacontainer:$BUILD_NUMBER'
      }
    }
    stage('Deploy to k8s') {
      steps {
        sshagent(['sshkey']) {
          sh 'scp -o StrictHostKeyChecking=no pod-from-inside.yaml ubuntu@ec2-54-242-204-168.compute-1.amazonaws.com:/home/ubuntu'
          sh 'ssh ubuntu@ec2-54-242-204-168.compute-1.amazonaws.com kubectl create -f .'
          sh 'ssh ubuntu@ec2-54-242-204-168.compute-1.amazonaws.com kubectl create clusterrolebinding serviceaccounts-cluster-admin --clusterrole=cluster-admin --group=system:serviceaccounts'
          }
      }
    }
  }
}
