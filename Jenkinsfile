pipeline {
  agent any
  stages {
    stage('Run Docker') {
      steps {
        bat 'docker run --rm alpine echo Hello'
      }
    }
  }
}
