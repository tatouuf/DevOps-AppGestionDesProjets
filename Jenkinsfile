
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
                sh '''
                    docker build -t backend-app:latest backend
                    docker tag backend-app:latest itafhaamdi/backend-app:latest
                '''
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push itafhaamdi/backend-app:latest
                        docker logout
                    '''
                }
            }
        }

        stage('Deploy MySQL') {
            steps {
                sh '''
                    if ! docker ps --format '{{.Names}}' | grep -qx mysql; then
                        docker start mysql
                    fi
                    docker network connect itaf-stack_default mysql 2>/dev/null || true
                '''
            }
        }

        stage('Deploy Backend') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker pull itafhaamdi/backend-app:latest
                        docker logout

                        docker rm -f backend-app || true

                        docker run -d \
                            --name backend-app \
                            --network itaf-stack_default \
                            -p 8081:8080 \
                            -e SPRING_DATASOURCE_URL="jdbc:mysql://mysql:3306/test_db?createDatabaseIfNotExist=true" \
                            itafhaamdi/backend-app:latest
                    '''
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                    docker ps
                    sleep 15
                    docker logs --tail 40 backend-app
                '''
            }
        }
    }
}

