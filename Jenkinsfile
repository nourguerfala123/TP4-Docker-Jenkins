
pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds'
        DOCKERHUB_USER = 'nourguerfala123'
        CLIENT_IMAGE = 'myappreact-client'
        SERVER_IMAGE = 'myappreact-server'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Recuperation du projet depuis GitHub'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Construction des images Docker'

                sh 'docker build -t $DOCKERHUB_USER/$CLIENT_IMAGE:$BUILD_NUMBER ./Client'

                sh 'docker build -t $DOCKERHUB_USER/$SERVER_IMAGE:$BUILD_NUMBER ./Server'
            }
        }

        stage('Test') {
            steps {
                echo 'Verification des images Docker'

                sh 'docker image inspect $DOCKERHUB_USER/$CLIENT_IMAGE:$BUILD_NUMBER'

                sh 'docker image inspect $DOCKERHUB_USER/$SERVER_IMAGE:$BUILD_NUMBER'
            }
        }

        stage('Push') {
            steps {
                script {
                    withCredentials([
                        usernamePassword(
                            credentialsId: DOCKERHUB_CREDENTIALS,
                            usernameVariable: 'DOCKER_USER',
                            passwordVariable: 'DOCKER_PASS'
                        )
                    ]) {
                        sh '''
                            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin

                            docker push $DOCKERHUB_USER/$CLIENT_IMAGE:$BUILD_NUMBER

                            docker push $DOCKERHUB_USER/$SERVER_IMAGE:$BUILD_NUMBER

                            docker logout
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline executee avec succes !'
        }

        failure {
            echo 'Erreur lors de l execution de la pipeline.'
        }
    }
}

