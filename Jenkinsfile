pipeline {
    agent {
        docker {
            image 'maven:3.9.2-eclipse-temurin-17-alpine'
            args '-v /root/.m2:/root/.m2'
        }
    }
    environment {
        POM_DIR = "spring.boot.jenkins.demo"
        NEXUS_URL = "nexus:50010/repository/docker-snapshot/"
        NEXUS_CRED = ""
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
                        def image = docker.build("my-image:${env.BUILD_ID}")
                        image.push()
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