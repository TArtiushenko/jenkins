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
    - name: dockersock
      mountPath: /var/run/docker.sock
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
  volumes:
  - name: dockersock
    hostPath:
      path: /var/run/docker.sock
'''
        // defaultContainer 'docker' 
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
    environment {
        IMAGE_VERSION = 'latest'
    }
    stages {
      stage('Checkout') {
        steps {
          git branch: "$ref", changelog: false, url: 'https://github.com/TArtiushenko/test.git'		

        }
      }
      stage('Create tag') {
        steps {
          sh(returnStdout: true, script: '''#!/bin/bash
          if [$ref = 'dev']; then
          IMAGE_VERSION=dev-$(date +%s)
          fi
          ''')
        }
      }
      stage('Build') {
        steps {
          container('docker') {
            sh label: 'test', script: 'docker -v'
            sh label: 'build', script: 'docker build . -t test:latest'
          }
          echo "${IMAGE_VERSION}"
        }
      }
    }
}

