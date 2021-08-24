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
        stage('Build docker image'){
            steps {
                echo 'Building docker image'
                script{
                    dockerImage = docker.build image+"_test"
                }
            }
        }
        stage("Run the docker container"){
            steps{
                echo 'Runing the container'
                script{
                    dockerContainer = dockerImage.run('-p 80:8000 --name server')
                }
            }
        } 
    }
}
        stage('Build docker image'){
            steps{
                echo 'Building docker image'
                script{
                    dockerImage= docker.build image
                }
            }
        }
        stage('Test'){
            steps{
                script{
                    sh 'docker run --name test_container --entrypoint /bin/sh ' + image + ' -c "npm run coverage"'
                    sh 'docker cp test_container:/usr/src/app/mochawesome-report .'
                    sh 'docker cp test_container:/usr/src/app/coverage .'
                    sh 'docker rm test_container'
                }

                publishHTML([
                    allowMissing: false, 
                    alwaysLinkToLastBuild: false, 
                    keepAll: false, 
                    reportDir: 'coverage', 
                    reportFiles: 'index.html', 
                    reportName: 'Coverage report', 
                    reportTitles: ''])

                publishHTML([
                    allowMissing: false, 
                    alwaysLinkToLastBuild: false, 
                    keepAll: false, 
                    reportDir: 'mochawesome-report', 
                    reportFiles: 'mochawesome.html', 
                    reportName: 'Mocha report', 
                    reportTitles: ''])
            }
        }
        stage('Push the image') { 
            steps { 
                echo 'Pushing image'
                script { 
                    docker.withRegistry( '', DOCKER_CREDENTIALS) { 
                        dockerImage.push() 
                    } 
                } 
            }
        }
        stage("Run the docker container and remove docker image"){
            steps{
                echo 'Runing the container'
                script{
                    dockerContainer = dockerImage.run('-p 8085:8000 --name server')
                }
            }
        } 
        stage("Manual confirmation of the app"){
            input{
                message "Can you see the app deployed?"
            } 
            steps{
                script{
                    dockerContainer.stop()
                    sh "docker rmi " + image
                }
            }
        }
    }
}