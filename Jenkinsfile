pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        git 'repoUrl'
      }
    }
    stage('Compile') {
      parallel {
        stage('Compile') {
          steps {
            sh '#gradle javaCompile'
          }
        }
        stage('Helm Package') {
          steps {
            sh 'helm package'
          }
        }
      }
    }
    stage('Docker Push') {
      parallel {
        stage('Docker Push') {
          steps {
            sh 'Docker push'
          }
        }
        stage('Java Unit Test') {
          steps {
            sh 'unit tests'
          }
        }
        stage('Java Integration Tests') {
          steps {
            sh 'int tests'
          }
        }
      }
    }
    stage('Helm Install') {
      steps {
        sh 'helm install'
      }
    }
  }
}