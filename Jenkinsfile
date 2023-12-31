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
                sh 'docker build -t eyolego/lego-todo-app:$BUILD_NUMBER .'
            }
        }
        stage('Login to Docker Hub and Push Image') {
            steps{
                withCredentials([usernamePassword(credentialsId: 'docker-hub-eyolego',
                                                  passwordVariable: 'DOCKERHUB_PSW',
                                                  usernameVariable: 'DOCKERHUB_USR')]) {
                    sh 'echo $DOCKERHUB_PSW | docker login -u $DOCKERHUB_USR --password-stdin'
                    sh 'docker push eyolego/lego-todo-app:$BUILD_NUMBER'
                }
            }
        }
        stage("Smoke Test") {
            steps {
                script {
                    sh "sleep 60"
                    sh "curl -I 54.226.244.83:8000"
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


// pipeline{
//     agent any

//     environment {
//         TAG = "${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
//         max = 20
//         random_num = "${Math.abs(new Random().nextInt(max+1))}"
// //         docker_password = credentials('dockerhub_password')
//     }

//     stages{
//         stage("Workspace Cleanup") {
//             steps {
//                 dir("${WORKSPACE}") {
//                     deleteDir()
//                 }
//             }
//         }

//         stage('Checkout Git') {
//             steps {
//                 git branch: 'main', credentialsId: '1f538342-affb-4763-b4dd-70aa119ae380', url: 'https://github.com/eyolegoo/P20-php-todo.git'
//             }
//         }

//         stage('Building application ') {
//             steps {
//                 script {
                    
//                     sh "  docker build -t todo-app ."
//                 }
//             }
//         }

//         stage('Creating docker container') {
//             steps {
//                 script {
//                     sh " docker run --name lego-todo-app --network tooling_app-network  -p 8080:8000 -it -d todo-app"
//                 }
//             }
//         }

//         stage("Smoke Test") {
//             steps {
//                 script {
//                     sh "sleep 60"
//                     sh "curl -I 54.226.244.83:8000"
//                 }
//             }
//         }

//         stage("Publish to Registry") {
//             steps {
//                 script {
//                     sh "  docker push eyolego/lego-todo-app "
//                 }
//             }
//         }

//         stage ('Clean Up') {
//             steps {
//                 script {
//                     sh " docker stop todo-app-${env.random_num}"
//                     sh " docker rm todo-app-${env.random_num}"
//                     sh " docker rmi eyolego/lego-todo-app"
//                 }
//             }
//         }

//         stage ('logout Docker') {
//             steps {
//                 script {
//                     sh " docker logout"
//                 }
//             }
//         }
//     }
   
// }