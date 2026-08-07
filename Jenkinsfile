pipeline {
    agent {
    docker {
        image 'node:20'
        args '-v /var/run/docker.sock:/var/run/docker.sock -u root'
    }
}
   stages {
        stage('Install docker') {
            steps {
                sh "apt-get update -y && apt-get install docker -y docker.io"
            }
        }
        stage('checkout') {
            steps {
                checkout scm
            }
        }
        stage('Dockerfile lint'){
            steps {
                sh "docker run --rm -i hadolint/hadolint < Dockerfile"
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
        stage ('Scan docker image for vulnabirites') {
            steps {
                script {
                    def imageName = env.BRANCH_NAME == 'main' ? 'kvara007/nodemain:v1.0' : 'kvara007/nodedev:v1.0'
                    sh "trivy image --exit-code 0 --cache-backend memory --severity LOW,MEDIUM,HIGH ${imageName}"
                }
            }
        }
        stage('trigger deploy') {
            steps {
                script {
                if (env.BRANCH_NAME == 'main') {
                    build job: 'Deploy_to_main', wait: false
                }
                else {
                        build job: "Deploy_to_dev", wait: false
                    }  
                }
            }
        }
    }
}
