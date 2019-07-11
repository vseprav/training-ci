pipeline {
  agent {
    node {
      label 'GoogleCloud'
    }

  }
  stages {
    stage('Run Tests') {
      steps {
        sh '''cd flask-app
docker-compose down
docker-compose build flask-app
docker-compose run flask-app pytest -v
docker-compose down'''
        sh '''cd flask-app
docker-compose run flask-app pytest -v --junit-xml=/var/opt/junit-report/report.xml'''
        junit 'flask-app/junit-report/report.xml'
        sh 'sudo rm -rf flask-app/junit-report'
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