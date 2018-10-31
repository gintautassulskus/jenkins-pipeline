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
                    image: jenkinsci/jnlp-slave:3.27-1-alpine
                    command:
                    - cat
                    tty: true
                  - name: git
                    image: alpine/git:1.0.4
                    command:
                    - cat
                    tty: true
                  - name: java
                    image: openjdk:8-jdk-slim
                    command:
                    - cat
                    tty: true
                  - name: docker
                    image: alpine:3.8
                    command:
                    - cat
                    tty: true
                  - name: helm
                    image: dtzar/helm-kubectl:2.11.0
                    command:
                    - cat
                    tty: true
                """
        }
    }
    options {
        // Because there's no way for the container to actually get at the git repo on the disk of the box we're running on.
        skipDefaultCheckout(true)
    }
    stages {
        stage('Checkout') {
            container('git') {
                steps {
                    checkout scm
                    stash name: 'sources', includes: '**', excludes: '**/.git,**/.git/**'
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