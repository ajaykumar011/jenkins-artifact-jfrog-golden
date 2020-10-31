pipeline {
    agent any
    options {
        timestamps()
    }
    environment {
        REGISTRY = "cloudzone.jfrog.io"
        IMAGE = "cloudzone.jfrog.io/jenkins-artifact-jfrog-golden"
        
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
                    def dockerfile = 'Dockerfile'
                    //image = docker.build("${IMAGE}")
                    customImage = docker.build("${IMAGE}:${env.BUILD_ID}")
                    
                    //docker push ${server-name}.jfrog.io/{repo-name}/<image name>
                    //docker.build(ARTIFACTORY_DOCKER_REGISTRY + '/jenkins-artifact-jfrog-golden:latest', 'dockerfiles')
                    //image = docker.build("${IMAGE}" + "jenkins-artifact-jfrog-golden:${env.BUILD_ID}", "-f ${dockerfile}")
                    println "Newly generated image, " + image.id
                }
            }
        }
        stage('test') {
            steps {
                script {
                    // https://hub.docker.com/repository/docker/ajaykumar011/docker-as-agent-in-jenkins
                    def container = image.run('-p 80')
                    def contport = container.port(80)
                    println image.id + " container is running at host port, " + contport
                    docker.withRegistry("${env.REGISTRY}", 'JFROG_WEB_CREDENTIALS') {
                            customImage.push()
                            customImage.push('latest')
                        }
                    }
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