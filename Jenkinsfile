pipeline {
    agent any

    tools{
        nodejs "njs"
        dockerTool "docker"
    }
    stages {
        stage("CI"){
            
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
            agent any

            tools {
                // Use the exact plugin class type string instead of the short name
                string(name: 'MySonarScanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation')
            }    
            stages{
                stage('validacion de codigo'){
                    steps{
                        withSonarQubeEnv('sqserver'){
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