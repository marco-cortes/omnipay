pipeline {
    agent any
    environment {
        PROJECT_KEY = "omnipay"
        DOCKER_IMAGE = "omnipay/api-gateway:latest"
    }
    stages {
        stage('⚙️ Preparación') {
            steps {
                cleanWs()
                checkout scm
            }
        }

        // Envolvemos los stages del API Gateway en un bloque superior
        stage('🚀 Build API Gateway') {
            // AQUÍ ESTÁ LA MAGIA: Solo se ejecuta si hay cambios en esta ruta
            when {
                changeset "backend/api-gateway/**"
            }
            stages {
                stage('🔍 Análisis SonarQube') {
                    steps {
                        script {
                            def scannerHome = tool 'SonarScanner'
                            withSonarQubeEnv('sonar-server') {
                                sh """
                                ${scannerHome}/bin/sonar-scanner \
                                    -Dsonar.projectKey=${PROJECT_KEY}-api-gateway \
                                    -Dsonar.projectName="Omnipay - API Gateway" \
                                    -Dsonar.sources=backend/api-gateway \
                                    -Dsonar.exclusions=**/node_modules/**
                                """
                            }
                        }
                    }
                }
                stage('🐳 Construir Imagen Docker') {
                    steps {
                        sh "docker build -t ${DOCKER_IMAGE} -f backend/api-gateway/Dockerfile backend/api-gateway/"
                    }
                }
            }
        }
        
        // Aquí agregarías en el futuro: stage('🚀 Build Ledger Service') { when { changeset "backend/ledger-service/**" } ... }
    }
}