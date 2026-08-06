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
                    def imageName = env.BRANCH_NAME == 'main' ? 'kvara007/nodemain:v1.0' : 'kvara007/nodedev:v1.0'
                    sh "docker build -t ${imageName} ."
                }
            }
        }
        stage ('push image') {
            steps {
                script {
                    def imageName = env.BRANCH_NAME == 'main' ? 'kvara007/nodemain:v1.0' : 'kvara007/nodedev:v1.0'
                    docker.withRegistry('', 'docker_creds') {
                        sh "docker push ${imageName}"
                    }
                }
            }
        }
        stage('trigger deploy') {
            script {
                if (env.BRANCH_NAME == 'main') {
                    build job: 'Deploy_to_main', wait: false
                }
                else if {
                    (env.BRANCH_NAME == 'main') {
                        build job: "Deploy_to_dev", wait: false
                    }  
                }
            }
        }
    }
}
