#!groovy

pipeline {
    agent any

    environment {
        MVN_HOME = tool name: 'Maven3', type: 'maven' // Ensure 'Maven3' exists in Global Tool Configuration
        JAVA_HOME = tool name: 'JDK8', type: 'jdk' // Ensure 'JDK8' is configured
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                sh "git clean -df"
                sh "git checkout -- ."
            }
        }

        stage('Build') {
            steps {
                withMaven(maven: 'Maven3', jdk: 'JDK8') {
                    sh "mvn install -DskipTests"
                }
            }
            post {
                success {
                    archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
                }
            }
        }

        stage('Test & Dependency Check') {
            parallel {
                stage('Test') {
                    steps {
                        withMaven(maven: 'Maven3', jdk: 'JDK8') {
                            sh "mvn surefire:test"
                        }
                    }
                }
                stage('Dependency Check') {
                    steps {
                        withMaven(maven: 'Maven3', jdk: 'JDK8') {
                            sh "mvn org.owasp:dependency-check-maven:check -Ddependency-check-format=XML"
                        }
                        dependencyCheckPublisher pattern: '**/target/dependency-check-report.xml'
                    }
                }
            }
        }
    }

    post {
        always {
            junit allowEmptyResults: true, testResults: '**/target/surefire-reports/TEST-*.xml'
        }
        failure {
            mail to: '$RECIPIENTS', subject: 'Build Failed', body: 'Check Jenkins for details.'
        }
    }
}
