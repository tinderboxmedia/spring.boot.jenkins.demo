pipeline {
    agent any
    environment {
        PROJECT_NAME = readMavenPom().getArtifactId()
        PROJECT_VERSION = readMavenPom().getVersion()
        
        NEXUS_URL = 'http://nexus:9000/repository/docker-images/'
        NEXUS_CRED = 'nexus-credentials'
    }
    stages {

        stage('Package') {
            agent {
                docker {
                    image 'maven:3-eclipse-temurin-17-alpine'
                    args '-v /root/.m2:/root/.m2'
                }
            }
            steps {
                sh 'mvn -B -ntp clean package'
            }
        }
        
        stage('Push') {
            agent any
            steps {
                script {
                    docker.withRegistry("${NEXUS_URL}", "${NEXUS_CRED}") {
                        def image = docker.build("${PROJECT_NAME}")
                        image.push("${PROJECT_VERSION}")
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }

    }
    options {
        skipStagesAfterUnstable()
        buildDiscarder(
            logRotator(
                artifactNumToKeepStr: '10',
                numToKeepStr: '10'
            )
        )
    }
}
