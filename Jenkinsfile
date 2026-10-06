pipeline {
    agent any
    tools {
        jdk 'JDK17'
        maven 'M2_HOME'
    }
    triggers {
        pollSCM('H/5 * * * *')
    }
    environment {
        IMAGE_NAME = 'oumaymaboutej/student-management'
        IMAGE_TAG  = "${env.BUILD_NUMBER}"
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
        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG -t $IMAGE_NAME:latest .'
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhubcredentials',
                        usernameVariable: 'DH_USER', passwordVariable: 'DH_TOKEN')]) {
                    sh 'echo $DH_TOKEN | docker login -u $DH_USER --password-stdin'
                    sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
                    sh 'docker push $IMAGE_NAME:latest'
                }
            }
        }
    }
    post {
        always {
            sh 'docker logout'
        }
        success {
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            echo 'Pipeline réussi : jar archivé et image publiée sur Docker Hub.'
        }
        failure {
            echo 'Pipeline en échec : consulter les logs et le rapport de tests.'
        }
    }
}
