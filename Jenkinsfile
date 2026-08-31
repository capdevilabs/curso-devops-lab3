pipeline {
    
    agent any
    
    stages {
        stage("CI"){
            agent {
                docker {
                    image "node:24"
                    reuseNode true
                }
            }
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