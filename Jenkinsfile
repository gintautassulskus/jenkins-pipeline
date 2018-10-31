pipeline {
    agent {
        kubernetes {
            label "mypod-${UUID.randomUUID().toString()}"
            defaultContainer 'jnlp'
            yaml """
                apiVersion: v1
                kind: Pod
                metadata:
                  labels:
                    class: KubernetesDeclarativeAgentTest
                spec:
                  containers:
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
            steps {
                container('git') {
                    checkout scm
                    stash name: 'sources', includes: '**', excludes: '**/.git,**/.git/**'
                }
            }
        }
        stage('Compile') {
            parallel {
                stage('Compile') {
                    steps {
                        container('java') {
                            unstash 'sources'
                            sh 'ls -la'
                            sh 'echo javaCompile'
                        }
                    }
                }
                stage('Helm Package') {
                    steps {
                        container('helm') {
                            unstash 'sources'
                            sh 'ls -la'
                            sh 'echo helm package'
                        }
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