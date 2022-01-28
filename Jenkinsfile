pipeline {
    agent {
        kubernetes {
            yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: docker
    image: docker
    command: 
     - "sleep"
    args:
     - "999999"
    volumeMounts:
    - mountPath: "/home/jenkins/agent"
      name: "workspace-volume"
      readOnly: false
    workingDir: "/home/jenkins/agent"
  - name: "jnlp"
    image: "jenkins/inbound-agent:4.3-4"
    resources:
      limits: {}
      requests:
        memory: "1042Mi"
        cpu: "500m"
    volumeMounts:
    - mountPath: "/home/jenkins/agent"
      name: "workspace-volume"
      readOnly: false
'''
        }
    }
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

    //  regexpFilterText: '$ref',
    //  regexpFilterExpression: 'dev'
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
            container('docker') {
                steps {
                    sh label: 'test', script: 'docker -v'
                }
            }
        }
    }
}

