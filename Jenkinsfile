pipeline {
    agent any

    
    stages {
        stage("CI"){
            agent {
                docker{
                    image 'node:24'
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
            agent{
                docker{
                    image 'sonarsource/sonar-scanner-cli:latest'
                    reuseNode true 
                }
            }    
            stages{
                stage('validacion de codigo'){
                    steps{
                        withSonarQubeEnv('sonarq-server'){
                            sh 'sonar-scanner'
                        }
                    }
                }
                stage('Validacion de puerta de calidad'){
                    options{
                        timeout(time: 1, unit: "MINUTES")
                    }
                    steps{
                        script{
                            def qualityGate = waitForQualityGate(); 
                            if(qualityGate.status != 'OK'){
                                error "La puerta de calidad ha fallado ${qualityGate.status}"
                            }
                        }
                    }
                }
            }
        }
        






        
    }
}