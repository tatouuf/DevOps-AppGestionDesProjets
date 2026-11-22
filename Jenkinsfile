
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/tatouuf/DevOps-AppGestionDesProjets.git'
            }
        }

        stage('Compile') {
            steps {
                dir('backend') {
                    sh 'mvn clean compile'
                }
            }
        }
    }
}
