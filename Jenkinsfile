pipeline {
  agent any

  environment {
    DOCKER_IMAGE = 'devops-app'
    DOCKERHUB_REPO = 'sumantharya/devops-app'
  }

  stages {
    stage('Build') {
      steps {
        sh 'mvn clean package'
      }
    }

    stage('Docker Build') {
      steps {
        sh 'docker build -t $DOCKER_IMAGE .'
      }
    }

    stage('Docker Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
          sh '''
            echo $PASSWORD | docker login -u $USERNAME --password-stdin
            docker tag $DOCKER_IMAGE $DOCKERHUB_REPO
            docker push $DOCKERHUB_REPO
          '''
        }
      }
    }

    stage('Deploy to EKS') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'aws-creds', usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
          sh '''
            aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
            aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY
            aws configure set region us-east-1

            aws eks update-kubeconfig --region us-east-1 --name devops-cluster

            kubectl apply -f deployment.yaml
          '''
        }
      }
    }
  }
}
