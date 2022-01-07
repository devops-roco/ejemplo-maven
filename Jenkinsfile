import groovy.json.JsonSlurperClassic
def jsonParse(def json) {
    new groovy.json.JsonSlurperClassic().parseText(json)
}
pipeline {
    agent any
    stages {
        stage("Paso 1: Descargar y cambiar a rama"){
            steps {
               checkout(
                        [$class: 'GitSCM',
                        branches: [[name: "pipeline-as-code" ]],
                        userRemoteConfigs: [[url: 'https://github.com/devops-roco/ejemplo-maven.git']]])
            }
        }
        stage("Paso 2: Compilar"){
            steps {
                script {
                sh "echo 'Compilando codigo!'"
                // Run Maven on a Unix agent.
                sh "mvn clean compile -e"
                }
            }
        }
        stage("Paso 3: Testear"){
            steps {
                script {
                sh "echo 'Ejecutando pruebas!'"
                // Run Maven on a Unix agent.
                sh "mvn clean test -e"
                }
            }
        }
        stage("Paso 4: Construir .Jar"){
            steps {
                script {
                sh "echo 'Construyendo .Jar!'"
                // Run Maven on a Unix agent.
                sh "mvn clean package -e"
                }
            }
        }
    }
    post {
        always {
            sh "echo 'fase always executed post'"
        }
        success {
            sh "echo 'Proceso exitoso'"
        }
        failure {
            sh "echo 'Proceso Fallido'"
        }
    }
}