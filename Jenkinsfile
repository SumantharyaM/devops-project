pipeline {
  agent any

  stages {
    stage('Build') {
      steps {
        sh 'mvn clean package'
      }
    }

    stage('Docker Build') {
      steps {
        sh 'docker build -t devops-app .'
      }
    }

    stage('Docker Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
          sh 'echo "" | docker login -u "" --password-stdin'
          sh 'docker tag devops-app $USERNAME/devops-app'
          sh 'docker push $USERNAME/devops-app'
        }
      }
    }
  }
}
