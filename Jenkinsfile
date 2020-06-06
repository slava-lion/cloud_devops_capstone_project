pipeline {
	agent any 
	stages {
		stage('Build') {
            steps {
                sh 'echo "Hello World"'
                sh '''
                    echo "Multiline shell steps works too"
                    ls -lah
                '''
            }
        }
        stage('Lint pyhton') {
            steps {
        	    sh 'pylint --disable=R,C,W1203,W1202 Docker/app.py'
            }
        }
		
	}
}