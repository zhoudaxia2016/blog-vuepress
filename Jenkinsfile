pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        yarn build
      }
    }
    stage('Deploy') {
      steps {
        cd /tmp
        ls
      }
    }
  }
}
