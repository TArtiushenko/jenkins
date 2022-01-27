pipeline {
    agent any
  triggers {
    GenericTrigger(
     genericVariables: [
      [key: 'ref', value: '$.ref']
     ],

     causeString: 'Triggered on $ref',

     token: 'ggtvymtvycerxw34t',
     tokenCredentialId: '',

     printContributedVariables: true,
     printPostContent: true,

     silentResponse: false,

     regexpFilterText: '$ref',
     regexpFilterExpression: 'refs/heads/dev'
    )
  }
    stages {
        stage('Checkout') {
            steps {
                sh "echo $ref"
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

