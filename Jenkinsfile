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
docker-compose run flask-app pytest -v --junit-xml=/var/opt/junit-report/report.xml
docker-compose down'''
        junit(testResults: 'flask-app/junit-report/report.xml', allowEmptyResults: true)
        sh 'sudo rm -rf flask-app/junit-report'
      }
    }
    stage('Run App') {
      steps {
        sh '''cd flask-app;
docker-compose down;
docker-compose up -d --build;'''
      }
    }
  }
}