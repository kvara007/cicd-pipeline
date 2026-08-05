pipeline {
    agent any

    stages {
        stage('checkout') {
            steps {
                checkout scm
            }
        }
        stage('build') {
            steps {
                sh 'chmod +x scripts/build.sh'
                sh './scripts/build.sh'
                }
            }
        stage('test') {
            steps {
                sh 'chmod +x scripts/test.sh'
                sh './scripts/test.sh'
                }
            }
        stage('build docker image') {
            steps {
                script {
                    def imageName = env.BRANCH_NAME == 'main' ? 'nodemain:v1.0.' : 'nodedev:v1.0.'
                    sh 'docker build -t ${imageName} .'
                }
            }
        }
        stage('deploy') {
            steps {
                sh 'docker stop $(docker ps -a -q)'
                sh 'docker rm $(docker ps -a -q)'
            script {
                if (env.BRANCH_NAME == 'main') {
                    sh 'docker run -d --expose 3000 -p 3000:3000 nodemain:v1.0'
                }
                else {
                    sh 'docker run -d --expose 3001 -p 3001:3000 nodedev:v1.0.'
                    }  
                }
            }
        }
    }
}
