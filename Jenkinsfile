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

        stage('Deploy App to k8s') {
            steps {
                withKubeConfig([credentialsId: 'config']) {
                    sh 'curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"'
                    sh 'chmod u+x ./kubectl'
                    sh './kubectl apply -f ./k8s.yaml'
                }
            }
        }
    }
}
