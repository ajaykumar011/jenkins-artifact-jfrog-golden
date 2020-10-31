pipeline {
    agent any
    environment{
        ARTIFACTORY_DOCKER_REGISTRY = "cloudzone.jfrog.io"        
    }
    stages {
        stage ('Clone') {
            steps {
                echo "cloning"
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
                    def dockerfile = 'Dockerfile'
                    docker.build(ARTIFACTORY_DOCKER_REGISTRY + '/jenkins-artifact-jfrog-golden:latest', 'dockerfiles')
                    //docker.build("jenkins-artifact-jfrog-golden:${env.BUILD_ID}", "-f ${dockerfile}" ./dockerfiles)
                    //def dockerfile = 'Dockerfile.jfrog'
                    //def customImage = docker.build(ARTIFACTORY_DOCKER_REGISTRY + '/jenkins-artifact-jfrog-golden:latest', "-f ${dockerfile} dockerfiles")
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
                    //host: 'tcp://127.0.0.1:1234',
                    targetRepo: 'local-repo', // where to copy to (from docker-virtual)
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



// =======

// ARTIFACTORY_DOCKER_REGISTRY should be IP/Artifactory-Repo-Key/Image:Tag
// HOST should be docker daemon (Docker for windows is localhost:2375)

//     stage('Build image') { // build and tag docker image
//         steps {
//             echo 'Starting to build docker image'

//             script {
//                 def dockerfile = 'Dockerfile'
//                 def customImage = docker.build('10.20.111.23:8081/docker-virtual/hello-world:latest', "-f ${dockerfile} .")

//             }
//         }
//     }

//     stage ('Push image to Artifactory') { // take that image and push to artifactory
//         steps {
//             rtDockerPush(
//                 serverId: "jFrog-ar1",
//                 image: "10.20.111.23:8081/docker-virtual/hello-world:latest",
//                 host: 'tcp://localhost:2375',
//                 targetRepo: 'local-repo', // where to copy to (from docker-virtual)
//                 // Attach custom properties to the published artifacts:
//                 properties: 'project-name=docker1;status=stable'
//             )
//         }
//     }