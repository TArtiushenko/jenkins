pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
		git branch: 'main', changelog: false, url: 'https://github.com/TArtiushenko/test.git'		
		ls -la
            }
        }
    }
}

