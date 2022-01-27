pipeline {
    agent any
  triggers {
    GenericTrigger(
     genericVariables: [
      [key: 'ref', value: '$.ref', regexpFilter: 'refs/heads/']
     ],

     causeString: 'Triggered on $ref',

     token: 'ggtvymtvycerxw34t',
     tokenCredentialId: '',

     printContributedVariables: true,
     printPostContent: true,

     silentResponse: false,

     regexpFilterText: '$ref',
     regexpFilterExpression: 'dev'
    )
  }
    stages {
        stage('Checkout') {
            steps {
		        git branch: "$ref", changelog: false, url: 'https://github.com/TArtiushenko/test.git'		
		        sh label: 'checkout', script: 'ls -la'
            }
        }
        stage('Build') {
            steps {
                sh label: 'test', script: 'ls -la'
            }
        }
    }
}

