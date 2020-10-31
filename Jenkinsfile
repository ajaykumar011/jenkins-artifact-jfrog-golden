pipeline {
    agent any
    options {
        timestamps()
    }
    environment {
        IMAGE = "jenkins-artifact-jfrog-golden"
        REGISTRY = "https://cloudzone.jfrog.io/artifactory/"
    }
    stages {
        stage('prep') {
            steps {
                script {
                    env.GIT_HASH = sh(
                        script: "git show --oneline | head -1 | cut -d' ' -f1",
                        returnStdout: true
                    ).trim()
                }
            }
        }
        stage('build') {
            steps {
                script {
                    image = docker.build("${IMAGE}")
                    println "Newly generated image, " + image.id
                }
            }
        }
        stage('test') {
            steps {
                script {
                    // https://hub.docker.com/repository/docker/ajaykumar011/docker-as-agent-in-jenkins
                    def container = image.run('-p 80')
                    //def contport = container.port(80) #use this if you know the port of container
                    println image.id + " container is running at host port, " + contport
                    docker.withRegistry("${env.REGISTRY}", 'JFROG_WEB_CREDENTIALS') {
                            image.push("${GIT_HASH}")
                            if ( "${env.BRANCH_NAME}" == "main" ) {
                                image.push("LATEST")
                            }
                        }
                    currentBuild.result = "SUCCESS"
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}