pipeline {
    environment {
        registry = 'venkatveginati/dockertest1'
        registryCredential = 'DockerHub'
        dockerSwarmManage = '10.40.1.26:2375'
        dockerhost = '10.40.1.26'
        dockerImage = ''
    }
    agent any
    stages {
        stage('Cloning our Git') {
            steps {
                git 'https://github.com/venkatveginati/dockertest1.git'
            }
        }
       stage ('Docker Build') {
        steps{
            script{
     // Build and push image with Jenkins' docker-plugin
    
        withDockerRegistry([credentialsId: "dockerhub", url: "https://index.docker.io/v1/"]) {
        image = docker.build("venkatveginati/dockertest1", "MyAwesomeApp")
        image.push()    
        }
      
            }
        }
    }
        stage('Cleaning up') {
            steps {
                sh "docker rmi $registry:v$BUILD_NUMBER"
            }
        }
        stage('Deploying to Docker Swarm') {
            steps {
                sh "docker -H tcp://$dockerSwarmManager service rm testing1 || true"
                sh "docker -H tcp://$dockerSwarmManager service create --name testing1 -p 8100:80 $registry:v$BUILD_NUMBER"
            }
        }
        stage('Verifying The Deployment') {
            steps {
                sh 'curl http://$dockerhost:8100 || exit 1'
                }
        }
    }
}
