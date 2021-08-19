pipeline {
    environment {
            registry = 'broosted1/proj3'
            registryCredential = 'dockerhub_id'
            dockerImage = ''
    }
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Set Docker') {
            steps {
                ARG DOCKER_CLIENT=docker-17.06.2-ce.tgz
                RUN cd /tmp/
                && curl -sSL -O https://download.docker.com/linux/static/stable/x86_64/${DOCKER_CLIENT} \
                && tar zxf ${DOCKER_CLIENT} \
                && mkdir -p /usr/local/bin \
                && mv ./docker/docker /usr/local/bin \
                && chmod +x /usr/local/bin/docker \
                && rm -rf /tmp/*
            }
        }
        stage('Building our image') {
            steps {
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Deploy our image') {
            steps {
                script {
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Cleaning up') {
            steps {
                sh "docker rmi $registry:$BUILD_NUMBER"
            }
        }
    }
}
