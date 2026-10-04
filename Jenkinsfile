pipeline {
    agent any
    tools {
        jdk 'JDK17'
        maven 'M2_HOME'
    }
    triggers {
        pollSCM('H/5 * * * *')
    }
    stages {
        stage('Commit') {
            steps {
                checkout scm
                sh 'git log -1 --pretty=format:"%h | %an | %s"'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Test unitaire') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
    }
    post {
        success {
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            echo 'Pipeline réussi : jar archivé.'
        }
        failure {
            echo 'Pipeline en échec : consulter les logs et le rapport de tests.'
        }
    }
}