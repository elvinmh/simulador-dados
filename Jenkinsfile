pipeline {
    agent any

    environment {
        registry = "elvinmh/simulador-dados:actividad2"
        registryCredentials = "dockerhub"
        project = "simulador-dados-actividad3"
        projectVersion = "1.0"
        repository = "https://github.com/elvinmh/simulador-dados.git"
        repositoryCredentials = "github"
    }

    stages {
        stage('Clean workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout code') {
            steps {
                script {
                    git branch: 'master',
                        credentialsId: repositoryCredentials,
                        url: repository
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    dockerImage = docker.build("${registry}")
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    echo "Ejecutando prueba en contenedor temporal..."
                    sh "docker run --rm --name ${project} ${registry}"
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    docker.withRegistry('', registryCredentials) {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Cleaning up') {
            steps {
                script {
                    echo "Limpiando imagen local..."
                    sh "docker rmi ${registry}"
                }
            }
        }

        // Opcional
        /*
        stage('Análisis SonarQube') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'sonar-scanner -Dsonar.projectKey=simulador-dados -Dsonar.sources=. -Dsonar.host.url=http://localhost:9000 -Dsonar.login=admin'
                }
            }
        }
        */
    }

    post {
        failure {
            echo "El pipeline ha fallado."
        }
    }
}
