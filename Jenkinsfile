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
                git(
                    branch: 'master',
                    credentialsId: repositoryCredentials,
                    url: repository
                )
            }
        }

        stage('SonarQube Analysis') {
            environment {
                scannerHome = tool 'Sonar'
            }
            steps {
                script {
                    withSonarQubeEnv('Sonar') {
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=${project} \
                            -Dsonar.projectName=${project} \
                            -Dsonar.projectVersion=${projectVersion} \
                            -Dsonar.sources=.
                        """
                    }
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
                    sh "docker rmi ${registry} || true"
                }
            }
        }
    }

    post {
        failure {
            echo "El pipeline ha fallado."
        }
    }
}
