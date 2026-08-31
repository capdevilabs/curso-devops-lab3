pipeline {
    agent any

    tools {
        nodejs "njs"
        dockerTool "docker"
    }
    
    stages {
        stage("CI"){
            stages{
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
                        sh 'npm run test -- --coverage'
                    }
                }
                stage("Build"){
                    steps {
                        sh 'npm run build'
                    }
                }

            }
        }
        

    }
}