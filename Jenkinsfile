pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials')
    }

    stages {
        stage('Cloner le repo') {
            steps {
                git branch: 'main', url: 'https://github.com/SlAbd/atelier4/'
            }
        }

        stage('Build') {
            steps {
                echo "Building the application..."
                bat 'docker build -t my-python-app .'
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                // Exemple : bat 'pytest tests/'
            }
        }

        stage('Pubat to Docker Hub') {
            steps {
                echo "Pubating the Docker image to Docker Hub..."
                bat '''
                    docker login -u $DOCKER_HUB_CREDENTIALS_USR -p $DOCKER_HUB_CREDENTIALS_PSW
                    docker tag my-python-app $DOCKER_HUB_CREDENTIALS_USR/my-python-app:latest
                    docker pubat $DOCKER_HUB_CREDENTIALS_USR/my-python-app:latest
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying the application..."
                bat '''
                    sbat user@remote-server "docker pull $DOCKER_HUB_CREDENTIALS_USR/my-python-app:latest"
                    sbat user@remote-server "docker stop my-python-app || true"
                    sbat user@remote-server "docker rm my-python-app || true"
                    sbat user@remote-server "docker run -d -p 5000:5000 --name my-python-app $DOCKER_HUB_CREDENTIALS_USR/my-python-app:latest"
                '''
            }
        }
    }
}
