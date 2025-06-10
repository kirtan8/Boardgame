pipeline {
  agent any

  tools {
    maven 'maven3'
  }

  stages {
    stage('Clone/SCM Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/kirtan8/Boardgame.git'
      }
    }

    stage('Build') {
      steps {
        // corrected "delpoy" to "deploy" and added ".xml" to settings file
        sh 'mvn clean install'
      }
    }
    stage('Docker Build & Push') {
  steps {
    script {
      dockerImage = "cary01/boardgame:latest"
      sh "docker build -t ${dockerImage} ."

      withCredentials([usernamePassword(credentialsId: 'docker-cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
        sh 'docker login -u $DOCKER_USER -p $DOCKER_PASS'
        sh "docker push ${dockerImage}"
         }
        }
      }
    }
     stage('Deploy to kubernetes') {
            steps {
                withKubeConfig(caCertificate: '', clusterName: 'devops-cluster', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://B7533D666453B797A58557D217809F18.gr7.ap-south-1.eks.amazonaws.com') {
                  sh "kubectl apply -f deployment-service.yaml -n webapps"
                }
            }
        }
        stage('check the deployment') {
            steps {
                withKubeConfig(caCertificate: '', clusterName: 'devops-cluster', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://B7533D666453B797A58557D217809F18.gr7.ap-south-1.eks.amazonaws.com') {
                  sh "kubectl get pods -n webapps"
                  sh "kubectl get svc -n webapps"
                }
            }
        }
    }
}
