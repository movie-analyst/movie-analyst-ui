pipeline {
    agent any
    environment {
		GIT_CREDENTIALS = credentials ('git-credentials')
        DOCKER_CREDENTIALS = 'docker-credentials'
        image = "juancastaneda20/movie-analyst-ui" + ":$BUILD_NUMBER"
        dockerImage = ''
        dockerContainer=''
	}
    stages {
        stage('Clone git repo') {
            steps {
                echo 'Cloning git repository'
                git credentialsId: 'git-credentials', url: 'git@gitlab.com:movie-analyst20/movie-analyst-ui.git'
            }
        }
        stage('build'){
            steps {
                echo 'Building the docker image'
                script{
                    dockerImage = docker.build image+"_test"
                }
            }
        }
        stage('test'){
            steps{
                echo 'Runing the tests'
            }
        }
    }
}