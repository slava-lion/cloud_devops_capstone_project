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
                        touch ~/dockerHubPassword
                        chmod 777 ~/dockerHubPassword
                        echo "$DOCKER_PASSWORD" > ~/dockerHubPassword

                        docker login --username slavalion --password-stdin < /home/ubuntu/dockerHubPassword
						
                        docker tag priceprediction slavalion/priceprediction
						docker push slavalion/priceprediction
					'''
				}
			}
		}
        /*stage('Create kubernetes cluster') {
			steps {
				withAWS(region:'us-west-2', credentials:'aws-esk') {
					sh '''
						eksctl create cluster \
                            --name capstoneEks \
                            --version 1.16 \
                            --nodegroup-name standard-workers \
                            --node-type t2.small \
                            --nodes 2 \
                            --nodes-min 1 \
                            --nodes-max 3 \
                            --node-ami auto \
                            --region us-west-2 \
                            --zones us-west-2a \
                            --zones us-west-2b \
                            --zones us-west-2c \
                        
                        aws eks --region us-west-2 update-kubeconfig --name capstoneEks
					'''
				}
			}
		}*/
        stage('Config kubectl context') {
			steps {
				withAWS(region:'us-west-2', credentials:'aws-esk') {
					sh '''
                        aws eks --region us-west-2 update-kubeconfig --name capstoneEks
						kubectl config use-context arn:aws:eks:us-west-2:980543251014:cluster/capstoneEks
					'''
				}
			}
		}
        stage('Blue deploy') {
			steps {
				withAWS(region:'us-west-2', credentials:'aws-esk') {
					sh '''
						kubectl apply -f ./blue-controller.json
					'''
				}
			}
		}
        stage('Green deploy') {
			steps {
				withAWS(region:'us-west-2', credentials:'aws-esk') {
					sh '''
						kubectl apply -f ./green-controller.json
					'''
				}
			}
		}
        stage('Load balancer for redirection to blue') {
			steps {
				withAWS(region:'us-west-2', credentials:'aws-esk') {
					sh '''
						kubectl apply -f ./blue-service.json
					'''
				}
			}
		}
	}
}