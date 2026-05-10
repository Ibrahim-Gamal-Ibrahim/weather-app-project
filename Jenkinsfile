pipeline {
    agent {
        kubernetes {
            yaml '''
            apiVersion: v1
            kind: Pod
            spec:
              containers:
              - name: kubectl
                image: bitnami/kubectl:latest
                command:
                - sleep
                args:
                - 99d
              serviceAccountName: jenkins-agent
            '''
        }
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Deploy Authentication System') {
            steps {
                container('kubectl') {
                    sh 'kubectl apply -f kubernetes/authentication/mysql'
                    sh 'kubectl apply -f kubernetes/authentication'
                }
            }
        }
        stage('Deploy Weather System') {
            steps {
                container('kubectl') {
                    sh 'kubectl apply -f kubernetes/weather'
                }
            }
        }
        stage('Deploy UI System') {
            steps {
                container('kubectl') {
                    sh 'kubectl apply -f kubernetes/ui'
                }
            }
        }
    }
}
