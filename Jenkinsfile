
pipeline {
    environment {
        registry = 'cmuriukin/docker-k8s'

        registryCredential = 'GIT_HUB_CREDENTIALS'

        dockerImage = ''
    }

    agent any

    stages {
        stage('Cloning our Git') {
      steps {
        git credentialsId: 'GIT_HUB_CREDENTIALS', url: 'https://github.com/cmuriukin/docker-k8s.git', branch: 'main'
      }
        }

        stage('Building our image') {
      steps {
        script {
          /*dockerImage = docker.build registry + ":$BUILD_NUMBER" */
          sh 'docker build -t devops-k8s:latest . '
        }
      }
        }

        stage('Deploy our image') {
      steps {
        script {
          /* groovylint-disable-next-line NestedBlockDepth
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push() */
          /* groovylint-disable-next-line NestedBlockDepth
            sh withCredentials([string(credentialsId: 'DOCKER_HUB_PASSWORD', variable: 'dockerhubpwd')]) {
             groovylint-disable-next-line GStringExpressionWithinString
            sh 'docker login -u cmuriukin -p ${dockerhubpwd}'
            }
            sh 'docker push devops-k8s:latest' */
            script {
                    // CUSTOM REGISTRY
                    docker.withRegistry('https://hub.docker.com/') {
                        /* Build the container image */
                        /* groovylint-disable-next-line NoDef, VariableTypeRequired */
                        def dockerImage = docker.build("docker-k8s:${env.BUILD_ID}")

                        /* Push the container to the custom Registry */
                        /* groovylint-disable-next-line GStringExpressionWithinString */
                        dockerImage.push("cmuriukin/docker-k8s:${env.BUILD_ID}")
                    }
                    /* Remove docker image
                    sh 'docker rmi -f my-image:${env.BUILD_ID}'   */
            }
          }
        }
      }
        }
    }

