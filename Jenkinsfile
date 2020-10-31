pipeline {
    agent any
    options {
        timestamps()
    }
    environment {
        REGISTRY = "https://cloudzone.jfrog.io/"
        IMAGE = "cloudzone.jfrog.io/jenkins-artifact-jfrog-golden/hello-world"
        // Hello-world the real file where jenkins-artifcat-jfrog-golden is storageor artifact.
        
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
                    customImage = docker.build("${IMAGE}:${env.BUILD_ID}")
                    println "Newly generated image, " + customImage.id
                }
            }
        }
        stage('test') {
            steps {
                script {
                    // https://cloudzone.jfrog.io/artifactory/jenkins-artifact-jfrog-golden/hello-world/
                    def container = customImage.run('-p 80')
                    def contport = container.port(80)
                    println customImage.id + " container is running at host port, " + contport
                    docker.withRegistry("${env.REGISTRY}", 'Jfrog-cred') {
                            customImage.push()
                            customImage.push('latest')
                        }
                    }
                }   
            }
    }

    post {
        always {
            echo "Clearing space"
            cleanWs()
        }
    }
}