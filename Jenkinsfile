pipeline {
  agent {
    node {
      label 'node 1'
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
        sh 'docker-compose up -d --build'
      }
    }
  }
}