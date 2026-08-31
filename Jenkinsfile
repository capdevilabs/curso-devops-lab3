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
        stage("QA"){
            agent {
                docker {
                    image 'sonarsource/sonar-scanner-cli'
                    reuseNode true
                }

            
            }
            stages{
                stage("validacion de codigo"){
                    steps{
                        withSonarQubeEnv('sonarqube'){
                            'sh sonar-scanner'
                        }
                    }
                }
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