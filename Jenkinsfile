pipeline {
    agent any

    options {
        timeout(time: 1, unit: 'HOURS')
        buildDiscarder(logRotator(numToKeepStr: '10'))
        disableConcurrentBuilds()
    }

    stages {
        stage('Inizializzazione') {
            steps {
                echo '=== Inizializzazione della Pipeline Notification ==='
                sh 'java -version'
            }
        }

        stage('Build') {
            steps {
                echo '=== Compilazione Servizio Notifiche (Spring Boot) ==='
                sh 'chmod +x mvnw'
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('Build Immagine Docker') {
            steps {
                echo '=== Creazione immagine Docker ==='
                sh 'docker build -t ghcr.io/blue-crystal-chicken/bcc-notification:latest .'
            }
        }

        stage('Push GHCR') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'ghcr-credentials', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login ghcr.io -u $USER --password-stdin'
                    sh 'docker push ghcr.io/blue-crystal-chicken/bcc-notification:latest'
                }
            }
        }
    }

    post {
        success { echo '=== Notification: pipeline completata con successo! ===' }
        failure { echo '=== Notification: errore durante la pipeline ===' }
    }
}
