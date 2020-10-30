pipeline {
    agent any
    environment{
        ARTIFACTORY_DOCKER_REGISTRY = "cloudzone.jfrog.io"        
    }
    stages {
        stage ('Clone') {
            steps {
                //git branch: 'main', url: 'https://github.com/ajaykumar011/jenkins-artifact-jfrog-golden/'
                //git branch: 'master', credentialsId: 'bitbucket-cred', url: 'https://bitbucket.org/jenkins-all-in-one/just-test/'
            }
        }

        stage ('Artifactory configuration') {
            steps {
                rtServer (
                    id: "CLOUDZONE_ARTIFACTORY_SERVER",
                    url: "https://cloudzone.jfrog.io/artifactory/jenkins-artifact-jfrog-golden/",
                    credentialsId: 'JFROG_WEB_CREDENTIALS',
                    bypassProxy: true,
                    timeout: 300
                )
            }
        }

        stage ('Build docker image') {
            steps {
                script {
                    def dockerfile = 'Dockerfile.jfrog'
                    docker.build(ARTIFACTORY_DOCKER_REGISTRY + '/jenkins-artifact-jfrog-golden:latest', 'dockerfiles')
                    //docker.build("jenkins-artifact-jfrog-golden:${env.BUILD_ID}", "-f ${dockerfile} ./dockerfiles")
                }
            }
        }

        stage ('Push image to Artifactory') {
            steps {
                rtDockerPush(
                    serverId: "CLOUDZONE_ARTIFACTORY_SERVER",
                    image: ARTIFACTORY_DOCKER_REGISTRY + '/jenkins-artifact-jfrog-golden:latest',
                    // Host:
                    // On OSX: "tcp://127.0.0.1:1234"
                    // On Linux can be omitted or null
                    host: HOST_NAME,
                    targetRepo: 'docker-local',
                    // Attach custom properties to the published artifacts:
                    properties: 'project-name=docker1;status=stable'
                )
            }
        }

        stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: "CLOUDZONE_ARTIFACTORY_SERVER"
                )
            }
        }
    }
}