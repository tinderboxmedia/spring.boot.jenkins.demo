pipeline {
    agent {
        docker {
            image 'maven:3.9.2-eclipse-temurin-17-alpine'
            args '-v /root/.m2:/root/.m2'
        }
    }
    environment {
        POM_DIR = 'spring.boot.jenkins.demo'
        NEXUS_URL = 'nexus:50010/repository/docker-snapshots/'
        NEXUS_CRED = 'nexus-credentials'
        IMAGE_NAME = 'jenkins-demo'
    }
    stages {

        stage('Build') {
            steps {
                dir("${POM_DIR}"){
                    sh 'mvn -B -ntp -DskipTests clean package'
                }
            }
        }

        stage('Test') {
            steps {
                dir("${POM_DIR}"){
                    sh 'mvn -B -ntp test'
                }
            }
            post {
                always {
                    echo 'Reporting is skipped...'
                }
            }
        }
        
        stage('Push') {
            steps {
                script {
                    docker.withRegistry("${NEXUS_URL}", "${NEXUS_CRED}") {
                        def image = docker.build("${IMAGE_NAME}")
                        image.push("${env.BUILD_ID}")
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