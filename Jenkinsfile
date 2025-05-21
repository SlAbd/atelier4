pipeline {
    agent any

    environment {
        // Définir les variables d'environnement ici ne suffit pas pour utiliser username/password
        // Utilisation correcte avec withCredentials plus bas
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

        stage('Push to Docker Hub') {
            steps {
                echo "Pushing the Docker image to Docker Hub..."
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_HUB_CREDENTIALS_USR', passwordVariable: 'DOCKER_HUB_CREDENTIALS_PSW')]) {
                    bat '''
                        docker login -u %DOCKER_HUB_CREDENTIALS_USR% -p %DOCKER_HUB_CREDENTIALS_PSW%
                        docker tag my-python-app %DOCKER_HUB_CREDENTIALS_USR%/my-python-app:latest
                        docker push %DOCKER_HUB_CREDENTIALS_USR%/my-python-app:latest
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying the application..."
                // Remplacer `sbat` par `ssh` ou autre outil correct si tu fais un déploiement distant
                bat '''
                    ssh user@remote-server "docker pull %DOCKER_HUB_CREDENTIALS_USR%/my-python-app:latest"
                    ssh user@remote-server "docker stop my-python-app || true"
                    ssh user@remote-server "docker rm my-python-app || true"
                    ssh user@remote-server "docker run -d -p 5000:5000 --name my-python-app %DOCKER_HUB_CREDENTIALS_USR%/my-python-app:latest"
                '''
            }
        }
    }
}
