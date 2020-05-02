pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh '''
          yarn build
        '''
      }
    }
    stage('Deploy') {
      steps {
        sh '''
          cd /tmp
          ls
        '''
      }
    }
  }
}
