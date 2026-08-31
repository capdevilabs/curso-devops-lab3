def tagAndPush(String localImage, String repo, String registry, String credential) {

    docker.withRegistry(registry, credential) {
        sh "docker tag ${localImage} ${repo}:latest"
        sh "docker tag ${localImage} ${repo}:${env.BUILD_NUMBER}"
        sh "docker tag ${localImage} ${repo}:${env.APP_SEMANTIC_VERSION}"
        sh "docker push ${repo}:latest"
        sh "docker push ${repo}:${env.BUILD_NUMBER}"
        sh "docker push ${repo}:${env.APP_SEMANTIC_VERSION}"
    }

}


pipeline {
    agent any

    environment {
        IMAGE_NAME = "curso-devops-lab3"
        DH_REPO    = "capdevila/curso-devops-lab3"
        GHCR_REPO  = "ghcr.io/capdevilabs/curso-devops-lab3"
        K8S_NAMESPACE  = "acapdevila"
        K8S_DEPLOYMENT = "acapdevila-deployment"
        K8S_CONTAINER  = "contenedor-acapdevila"
    }
    
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
                    image 'sonarsource/sonar-scanner-cli'
                    args '--network devops-infra_default'
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

        stage("CD") {
            stages{
                stage("Build dockerfile") {
                    steps {
                        sh "docker build -t ${env.IMAGE_NAME} ."
                        script {
                            // if (!env.APP_SEMANTIC_VERSION?.trim()) {
                            //     error("APP_SEMANTIC_VERSION no definida en el stage anterior")
                            // }
                            // Aca llamamos a la funcion que definimos al principio , y ya esta funcion 
                            // hace login en dockerhub y github con docker.withRegistry y sube ambas imagenes
                            tagAndPush(env.IMAGE_NAME, env.DH_REPO, "https://docker.io/capdevila/", "dh-credencial")
                            tagAndPush(env.IMAGE_NAME, env.GHCR_REPO, "https://ghcr.io", "gh-credencial")
                        }
                    }
                } 

                stage("Despliegue continuo"){
                    agent {
                        docker {
                            image 'alpine/k8s:1.34.6'
                            reuseNode true
                        }
                    }
                    steps{
                        script {
                            if (!env.APP_SEMANTIC_VERSION?.trim()) {
                                error("APP_SEMANTIC_VERSION no definida para el despliegue")
                            }
                        }
                        withKubeConfig([credentialsId: 'credencial-k8']) {
                            sh """
                                kubectl -n ${env.K8S_NAMESPACE} set image deployment/${env.K8S_DEPLOYMENT} ${env.K8S_CONTAINER}=${env.DH_REPO}:${env.APP_SEMANTIC_VERSION}
                                kubectl -n ${env.K8S_NAMESPACE} rollout status deployment/${env.K8S_DEPLOYMENT}
                            """
                        }
                    }
                }  
            }
            
        }
        
        
    }
}