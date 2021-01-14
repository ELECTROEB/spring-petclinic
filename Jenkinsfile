pipeline {
    agent any

    tools {
        jdk 'jdk-11'
        maven 'mvn-3.6.3'
    }

    stages {
//        stage('SCM') {
//            steps {
//                git branch:'main', url:'https://github.com/ELECTROEB/spring-petclinic.git'
//            }
//        }

        stage('Build') {
            steps {
                withMaven(maven:'mvn-3.6.3') {
                    sh "mvn package"
                }
            }
            post {
                success {
                    junit '**/target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'target/*.jar'
                }
            }
        }

        stage('Create and push container') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    withMaven(maven: 'mvn-3.6.3') {
                        sh "mvn jib:build"
                    }
                }
            }
        }
    }
}
