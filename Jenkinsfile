pipeline {
    agent any

    environment {
        // Appends common Docker installation paths to the pipeline environment
        PATH = "/usr/local/bin:/usr/bin:/bin:${env.PATH}"
    }
    stages {
        stage("CI"){
            agent {
                docker {
                    image "node:24"
                    reuseNode true
                   
                }
            }
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