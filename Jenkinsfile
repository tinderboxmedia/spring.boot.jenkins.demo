pipeline {
    agent any
    environment {
        POM_DIR = 'spring.boot.jenkins.demo'
        NEXUS_URL = 'http://nexus:50010/repository/docker-snapshots/'
        NEXUS_CRED = 'nexus-credentials'
        IMAGE_NAME = 'jenkins-demo'
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
                dir("${POM_DIR}") {
                    sh 'mvn -B -ntp clean package'
                }
            }
        }
        
        stage('Push') {
            agent any
            steps {
                script {
                    sh "cp ${POM_DIR}/target/*.jar ${WORKSPACE}/target"
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