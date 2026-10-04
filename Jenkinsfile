        stage('Commit') {
            steps {
                checkout scm
                sh 'git log -1 --pretty=format:"%h | %an | %s"'
            }
        }