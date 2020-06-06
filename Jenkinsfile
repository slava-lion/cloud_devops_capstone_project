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
                sh 'cd Docker/'
        	    sh 'make install'
            }
        }
        stage('Lint docker and pyhton') {
            steps {
        	    sh 'cd Docker/'
        	    sh 'make lint'
            }
        }
		
	}
}