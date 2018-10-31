#!/usr/bin/env groovy
pipeline {
  agent {
    kubernetes {
      label "mypod-${UUID.randomUUID().toString()}"
      yaml """
                apiVersion: v1
                kind: Pod
                metadata:
                  labels:
                    class: KubernetesDeclarativeAgentTest
                spec:
                  containers:
                  - name: jnlp
                    env:
                    - name: CONTAINER_ENV_VAR
                      value: jnlp
                  - name: helm
                    image: lachlanevenson/k8s-helm:latest
                    command:
                    - cat
                    tty: true
                    env:
                    - name: CONTAINER_ENV_VAR
                      value: helm-pod
                """
    }
  }
  options {
    // Because there's no way for the container to actually get at the git repo on the disk of the box we're running on.
    skipDefaultCheckout(true)
  }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('Compile') {
      parallel {
        stage('Compile') {
          steps {
            sh 'echo javaCompile'
          }
        }
        stage('Helm Package') {
          steps {
            sh 'echo helm package'
          }
        }
      }
    }
    stage('Docker Push') {
      parallel {
        stage('Docker Push') {
          steps {
            sh 'echo Docker push'
          }
        }
        stage('Java Unit Test') {
          steps {
            sh 'echo unit tests'
          }
        }
        stage('Java Integration Tests') {
          steps {
            sh 'echo int tests'
          }
        }
      }
    }
    stage('Helm Install') {
      steps {
        sh 'echo helm install'
      }
    }
  }
}