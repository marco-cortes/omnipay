pipeline {
    // Ejecuta el pipeline en cualquier agente disponible (en este caso, tu contenedor Jenkins)
    agent any

    environment {
        // Definimos variables globales para el pipeline
        PROJECT_KEY = "omnipay"
        DOCKER_IMAGE = "omnipay/api-gateway:latest"
    }

    stages {
        stage('⚙️ Preparación') {
            steps {
                echo "Iniciando pipeline para OmniPay Flow..."
                // Limpiamos el espacio de trabajo para evitar basura de builds anteriores
                cleanWs()
                // Descargamos el código del repositorio
                checkout scm
            }
        }

        stage('🔍 Análisis SonarQube') {
            steps {
                // Requiere que la herramienta 'SonarScanner' esté configurada en Jenkins
                script {
                    def scannerHome = tool 'SonarScanner'
                    // 'sonar-server' es el nombre de la conexión que configuraremos en Jenkins
                    withSonarQubeEnv('sonar-server') {
                        sh """
                        ${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=${PROJECT_KEY} \
                            -Dsonar.projectName="OmniPay Flow" \
                            -Dsonar.sources=. \
                            -Dsonar.exclusions=**/node_modules/**,**/dist/**,**/.k8s/**
                        """
                    }
                }
            }
        }

        stage('🛡️ Quality Gate') {
            steps {
                // Jenkins pausará temporalmente hasta que SonarQube evalúe si el código pasa o falla
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('🐳 Build Docker Image') {
            steps {
                echo "Construyendo la imagen de prueba usando el socket de Docker..."
                // Gracias al volume mount (/var/run/docker.sock), esto compilará la imagen en tu máquina anfitriona
                sh "docker build -t ${DOCKER_IMAGE} -f backend/api-gateway/Dockerfile ."
            }
        }
    }

    post {
        // Acciones a realizar al finalizar el pipeline, sin importar el resultado
        always {
            echo "Pipeline finalizado. Limpiando credenciales y temporales..."
        }
        success {
            echo "✅ ¡Build exitoso! El código es seguro y la imagen está lista."
        }
        failure {
            echo "❌ El pipeline falló. Revisa los logs de SonarQube o de compilación."
        }
    }
}