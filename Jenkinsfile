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
                        sh 'npm run test'
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
            agent any
            environment {
                // "sonar-scanner-tool" must match the EXACT name configured in Global Tool Configuration
                SCANNER_HOME = tool 'sqscanner' 
            }
            stages{
                stage("validacion de codigo"){
                    steps{
                        withSonarQubeEnv('sonarqube'){
                            sh 'sonar-scanner'
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