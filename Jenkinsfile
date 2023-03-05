pipeline {
    agent any

    stages {
        stage('build') {
            steps {
                sh "ls"
                sh "docker ps"
            }
        }
    
        stage('deploy') {
            steps {
                sh "echo ${build_number}"
            }
        }
    }
}
