pipeline {
    agent  any
    stages {
        stage('SCM Checkout') {
            steps {
                script {
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: '*/main']],
                        userRemoteConfigs: [[url: 'https://github.com/eyolegoo/P20-php-todo.git']]
                    ])
                }
            }
        }
        stage('Build docker image') {
            steps {
                sh 'docker build -t eyolego/php-todo-app:$BUILD_NUMBER .'
            }
        }
        stage('Login to Docker Hub and Push Image') {
            steps{
                withCredentials([usernamePassword(credentialsId: 'docker-hub-eyolego',
                                                  passwordVariable: 'DOCKERHUB_PSW',
                                                  usernameVariable: 'DOCKERHUB_USR')]) {
                    sh 'echo $DOCKERHUB_PSW | docker login -u $DOCKERHUB_USR --password-stdin'
                    sh 'docker push eyolego/php-todo-app:$BUILD_NUMBER'
                }
            }
        }
        stage("Smoke Test") {
            steps {
                script {
                    sh "sleep 60"
                    sh "curl -I 44.203.191.92:8085"
                }
            }
        }
    }
    post {
        always {
            cleanWs(cleanWhenNotBuilt: false,
                    deleteDirs: true,
                    disableDeferredWipeout: true,
                    notFailBuild: true,
                    patterns: [[pattern: '.gitignore', type: 'INCLUDE'],
                               [pattern: '.propsfile', type: 'EXCLUDE']])
            sh 'docker logout'
        }
    }
}
