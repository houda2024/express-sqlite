pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dh_cred')
        DOCKER_IMAGE = "${DOCKERHUB_CREDENTIALS_USR}/express-sqlite-app:$BUILD_ID"
    }
    triggers {
        pollSCM('*/5 * * * *') // Vérifier toutes les 5 minutes
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                script {
                    // Construction de l'image Docker
                    docker.build(DOCKER_IMAGE, '.')
                }
            }
        }
        stage('Publish') {
            steps {
                script {
                    // Authentification et publication de l'image
                    withCredentials([usernamePassword(credentialsId: 'dh_cred', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                        sh "docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD"
                        sh "docker push $DOCKER_IMAGE"
                    }
                }
            }
        }
        stage('Cleanup') {
            steps {
                script {
                    // Nettoyage
                    docker.image(DOCKER_IMAGE).remove()
                    sh 'docker logout'
                }
            }
        }
    }
}
