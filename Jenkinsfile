pipeline{
    agent any

    environment {
        TAG = "${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
        max = 20
        random_num = "${Math.abs(new Random().nextInt(max+1))}"
        // DOCKERHUB_CREDENTIALS = credentials('dockerhub')
        
    }

    stages{
        stage("Workspace Cleanup") {
            steps {
                dir("${WORKSPACE}") {
                    deleteDir()
                }
            }
        }

        stage('Checkout Git') {
            steps {
                git branch: 'main', credentialsId: '648507ac-1beb-428a-bd01-f75a8b9f6e6a', url: 'https://github.com/teaguejobs/php-todo-proj20'
            }
        }

        stage('Building application ') {
            steps {
                script {

                     sh " docker login -u teaguejobs -p ${env.PASSWORD}"
                     sh " docker build -t teaguejobs/php-todo:${env.TAG} ."
                }
            }
        }
    

        stage('Creating docker container') {
            steps {
                script {
                    sh " docker run -d --name todo-app-${env.random_num} -p 8000:8000 teaguejobs/php-todo:${env.TAG}"
                }
            }
        }

        stage("Smoke Test") {
            steps {
                script {
                    sh "sleep 60"
                    sh "curl -I 52.91.29.79:8000"
                }
            }
        }

        stage("Publish to Registry") {
            steps {
                script {
                    sh " docker push teaguejobs/php-todo:${env.TAG}"
                }
            }
        }

        stage ('Clean Up') {
            steps {
                script {
                    sh " docker stop todo-app-${env.random_num}"
                    sh " docker rm todo-app-${env.random_num}"
                    sh " docker rmi teaguejobs/php-todo:${env.TAG}"
                }
            }
        }

        stage ('logout Docker') {
            steps {
                script {
                    sh " docker logout"
                }
            }
        }
    }
   
}
