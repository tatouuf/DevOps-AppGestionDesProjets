
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

        stage('Package') {
            steps {
                dir('backend') {
                    sh 'mvn package -DskipTests'
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t appgestion-backend:1.0 backend'
            }
        }

        stage('Docker Deploy') {
            steps {
                sh '''
                    docker rm -f appgestion-test || true

                    docker run -d \
                        --name appgestion-test \
                        --network itaf-stack_default \
                        -p 8081:8080 \
                        -e SPRING_DATASOURCE_URL="jdbc:mysql://mysql-db:3306/test_db?createDatabaseIfNotExist=true" \
                        appgestion-backend:1.0
                '''
            }
        }
    }
}

