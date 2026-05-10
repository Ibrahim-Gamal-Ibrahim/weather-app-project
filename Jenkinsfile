pipeline {
    agent {
        kubernetes {
            yaml '''
            apiVersion: v1
            kind: Pod
            metadata:
              namespace: jenkins
            spec:
              serviceAccountName: jenkins-agent
              containers:
              - name: git
                image: alpine/git:latest
                command:
                - sleep
                args:
                - 99d
              - name: kaniko
                image: gcr.io/kaniko-project/executor:debug
                command:
                - sleep
                args:
                - 99d
                volumeMounts:
                - name: docker-cred
                  mountPath: /kaniko/.docker/
              - name: kubectl
                image: bitnami/kubectl:latest
                command:
                - sleep
                args:
                - 99d
                securityContext:
                  runAsUser: 0
              volumes:
              - name: docker-cred
                secret:
                  secretName: docker-cred
            '''
        }
    }
    stages {
        stage('Checkout') {
            steps {
                container('git') {
                    checkout scm
                }
            }
        }
        stage('Build & Push UI') {
            steps {
                container('kaniko') {
                    sh '/kaniko/executor --context=`pwd`/UI --dockerfile=`pwd`/UI/Dockerfile --destination=ibrahimgamal10203040/weatherapp-ui:latest'
                }
            }
        }
        stage('Build & Push Auth') {
            steps {
                container('kaniko') {
                    sh '/kaniko/executor --context=`pwd`/auth --dockerfile=`pwd`/auth/Dockerfile --destination=ibrahimgamal10203040/weatherapp-auth:latest'
                }
            }
        }
        stage('Build & Push Weather') {
            steps {
                container('kaniko') {
                    sh '/kaniko/executor --context=`pwd`/weather --dockerfile=`pwd`/weather/Dockerfile --destination=ibrahimgamal10203040/weatherapp-weather:latest'
                }
            }
        }
        stage('Deploy Authentication System') {
            steps {
                container('kubectl') {
                    sh 'kubectl apply -f kubernetes/authentication/mysql -n default'
                    sh 'kubectl apply -f kubernetes/authentication -n default'
                }
            }
        }
        stage('Deploy Weather System') {
            steps {
                container('kubectl') {
                    sh 'kubectl apply -f kubernetes/weather -n default'
                }
            }
        }
        stage('Deploy UI System') {
            steps {
                container('kubectl') {
                    sh 'kubectl apply -f kubernetes/ui -n default'
                }
            }
        }
    }
}
