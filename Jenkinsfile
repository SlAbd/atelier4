pipeline {
    agent any

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

        stage('Push to Docker Hub') {
            steps {
                echo "Pushing the Docker image to Docker Hub..."
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_HUB_CREDENTIALS_USR', passwordVariable: 'DOCKER_HUB_CREDENTIALS_PSW')]) {
                    bat '''
                        echo %DOCKER_HUB_CREDENTIALS_PSW% | docker login -u %DOCKER_HUB_CREDENTIALS_USR% --password-stdin
                        docker tag my-python-app %DOCKER_HUB_CREDENTIALS_USR%/my-python-app:latest
                        docker push %DOCKER_HUB_CREDENTIALS_USR%/my-python-app:latest
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                echo "Déploiement local de l'application Docker..."
                bat '''
                    docker stop my-python-app || echo "Container not running"
                    docker rm my-python-app || echo "Container not found"
                    docker run -d -p 5000:5000 --name my-python-app my-python-app
                '''
            }
        }
    }
}
