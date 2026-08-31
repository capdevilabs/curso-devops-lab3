pipeline {
    agent any
    
    stages {
        stage("Instalar dependencias"){
            steps {
                sh 'npm install'
            }
        }
        stage("Ejecutar linter"){
            steps {
                sh 'npm run lint'
            }
        }
        stage("Ejecutar tests"){
            steps {
                sh 'npm run test'
            }
        }
        stage("Build"){
            steps {
                sh 'npm run build'
            }
        }
        stage("Imagen Docker"){
            steps {
                sh 'docker build -t curso-devops-lab3:latest'
                sh 'docker build -t curso-devops-lab3:1.0.0'
            }
        }

    }
}