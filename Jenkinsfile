
import groovy.json.JsonSlurperClassic
def jsonParse(def json) {
    new groovy.json.JsonSlurperClassic().parseText(json)
}
pipeline {
    agent any
    stages {
        stage("Paso 1: Compilar"){
            steps {
                script {
                sh "echo 'Compilacion de codigo!'"
                // Run Maven on a Unix agent.
                sh "mvn clean compile -e"
                }
            }
        }
        stage("Paso 2: Pruebas"){
            steps {
                script {
                sh "echo 'Ejecutando pruebas!'"
                // Run Maven on a Unix agent.
                sh "mvn clean test -e"
                }
            }
        }
        stage("Paso 3: Sonar"){
            steps {
                withSonarQubeEnv('sonarqube') {
                sh 'mvn clean package sonar:sonar'
              }
            }
        }
        stage("Paso 4: Construyendo .Jar"){
            steps {
                script {
                sh "echo 'Construyendo .Jar!'"
                // Run Maven on a Unix agent.
                sh "mvn clean package -e"
                }
            }
            post {
                //record the test results and archive the jar file.
                success {
                    archiveArtifacts artifacts:'build/*.jar'
                }
            }
        }
    }
    post {
        always {
            sh "echo 'Mensaje que siempre se ejecuta'"
        }
        success {
            sh "echo 'Proceso exitoso'"
        }
        failure {
            sh "echo 'Proceso fallido'"
        }
    }
}