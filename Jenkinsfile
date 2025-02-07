pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/sairam-bathini/dependency-check-test.git'
            }
        }

        stage('Run Dependency Check') {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'Dependency-Check'
            }
        }

        stage('Publish Report') {
            steps {
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
    }
}
