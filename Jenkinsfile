
import groovy.json.JsonSlurperClassic
def jsonParse(def json) {
    new groovy.json.JsonSlurperClassic().parseText(json)
}
pipeline {
    agent any
    environment {
        NEXUS_USER         = credentials('admin')
        NEXUS_PASSWORD     = credentials('droco1993')
    }
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
                sh "mvn clean test"
                }
            }
        }
        stage("Paso 3: Build .Jar"){
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
        stage("Paso 4: Análisis SonarQube"){
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh "echo 'Ejecutando analisis de SonarQube!'"
                    // Run Maven on a Unix agent to execute Sonar.
                    sh 'mvn sonar:sonar'
                }
            }
            post {
                //record the test results and archive the jar file.
                success {
                    //archiveArtifacts artifacts:'build/*.jar'
                    nexusPublisher nexusInstanceId: 'nexus',
                        nexusRepositoryId: 'devops-usach-nexus',
                        packages: [
                            [$class: 'MavenPackage',
                                mavenAssetList: [
                                    [classifier: '',
                                    extension: '.jar',
                                    filePath: 'build/DevOpsUsach2020-0.0.1.jar']
                                ],
                        mavenCoordinate: [
                            artifactId: 'DevOpsUsach2020',
                            groupId: 'com.devopsusach2020',
                            packaging: 'jar',
                            version: '0.0.1']
                        ]
                    ]
                }
            }
        }
        stage("Paso 5: Descarga desde Nexus") {
            steps {
                //http://nexus3:10003/repository/devops-usach-nexus/com/devopsusach2020/DevOpsUsach2020/0.0.2/DevOpsUsach2020-0.0.2.jar
                sh ' curl -X GET -u $NEXUS_USER:$NEXUS_PASSWORD "http://nexus:8081/repository/devops-usach-nexus/com/devopsusach2020/DevOpsUsach2020/0.0.1/DevOpsUsach2020-0.0.1.jar" -O'
            }
        }
        stage("Paso 6: Levantar Springboot APP"){
            steps {
                sh 'nohup bash java -jar DevOpsUsach2020-0.0.1.jar & >/dev/null'
            }
        }
        stage("Paso 7: Dormir(Esperar 10sg) "){
            steps {
                sh 'sleep 10'
            }
        }
        stage("Paso 8: Test Alive Service - Testing Application!"){
            steps {
                sh 'curl -X GET "http://localhost:8081/rest/mscovid/test?msg=testing"'
            }
        }
        stage("Subir nueva Version"){
            steps {
                //archiveArtifacts artifacts:'build/*.jar'
                nexusPublisher nexusInstanceId: 'nexus',
                    nexusRepositoryId: 'devops-usach-nexus',
                    packages: [
                        [$class: 'MavenPackage',
                            mavenAssetList: [
                                [classifier: '',
                                extension: '.jar',
                                filePath: 'DevOpsUsach2020-0.0.1.jar']
                            ],
                    mavenCoordinate: [
                        artifactId: 'DevOpsUsach2020',
                        groupId: 'com.devopsusach2020',
                        packaging: 'jar',
                        version: '1.0.0']
                    ]
                ]
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
