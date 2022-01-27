pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo $branch
		        git branch: 'main', changelog: false, url: 'https://github.com/TArtiushenko/test.git'		
		        sh label: 'checkout', script: 'ls -la'
            }
        }
        stage('Test') {
            steps {
                sh label: 'test', script: 'ls -la'
            }
        }
    }
}

