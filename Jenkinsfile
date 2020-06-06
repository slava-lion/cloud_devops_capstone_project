pipeline {
	agent any 
	stages {
		stage('Build') {
            steps {
                sh 'echo "Hello World"'
                sh '''
                    echo "Multiline shell steps works too"
                    ls -lah

                    cd Docker/
                    make install
                '''
            }
        }
        stage('Lint docker and pyhton') {
            steps {
        	    sh '''
                    cd Docker/
        	        make lint
                '''
            }
        }
		stage('Build Docker Image') {
			steps {
				sh '''
                    cd Docker/
                    bash build_docker.sh
                '''
			}
		}
        stage('Push Image To Dockerhub') {
			steps {
				withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'DockerHub', usernameVariable: 'DOCKER_USER ', passwordVariable: 'DOCKER_PASSWORD ']]){
					sh '''
						docker login -u $DOCKER_USER  -p $DOCKER_PASSWORD 
                        docker tag priceprediction slavalion/priceprediction
						docker push slavalion/priceprediction
					'''
				}
			}
		}
	}
}