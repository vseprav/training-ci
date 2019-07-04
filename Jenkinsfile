pipeline {
  agent {
    node {
      label 'slave'
    }

  }
  stages {
    stage('Git Hello') {
      steps {
        sh 'echo "Hello World"'
      }
    }
    stage('Run App') {
      steps {
        sh '''cd flask-app;
docker-compose up -d --build'''
      }
    }
  }
}