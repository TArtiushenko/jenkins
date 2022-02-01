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
      [key: 'ref', value: '$.ref', regexpFilter: 'refs/heads/', default: ''],
      [key: 'commiter', value: '$.head_commit.committer.username'],
      // [key: 'base_ref', value: '$.base.ref', default: '']
     ],

     causeString: 'Triggered on $ref',

     token: 'ggtvymtvycerxw34t',
     tokenCredentialId: '',

     printContributedVariables: true,
     printPostContent: true,

     silentResponse: false,

    regexpFilterText: '$commiter',
    regexpFilterExpression: '^(?!.*(TarasArtiushenko))'
    )
  }
    environment {
        GCR_DOMAIN = 'gcr.io'
        GCP_PROJECT = 'valid-volt-331607'
        IMAGE_NAME = 'test-jenkins'
        IMAGE_VERSION = 'latest'
        SKIP = false
    }
    stages {
      stage('Checkout') {
        steps {
          script {
            if (ref.contains('refs/tags/')) { 
              tag = ref.replace('refs/tags/', '')
              checkout([$class: 'GitSCM', branches: [[name: '*/tags/' + tag]], extensions: [], userRemoteConfigs: [[refspec: '+refs/tags/*:refs/remotes/origin/tags/*', url: 'https://github.com/TArtiushenko/test.git']]])
            } else {
              withCredentials([string(credentialsId: 'git_access_token', variable: 'access_token')]) {
                git branch: ref, changelog: false, url: 'https://' + access_token + '@github.com/TArtiushenko/test.git'
              }
            }
          }
        }
      }

      stage('Create tag') {
        steps {
          script {
            switch("$ref") {
              case 'dev':
                IMAGE_VERSION = 'dev-' + sh(returnStdout: true, script: 'date +%s')
              break

              case 'release':
                IMAGE_VERSION = sh(returnStdout: true, script: '''(git tag | egrep rc-[0-9] || echo rc-0 ) | sort --version-sort -r | head -1 | awk -F 'rc-' '{ print "rc-" $2 +1}' ''')
              break

              case 'main':
                IMAGE_VERSION = sh(returnStdout: true, script: '''(git tag | egrep v[0-9]\\.[0-9]\\.[0-9] || echo v1.0.-1 ) | sort --version-sort -r | head -1 | awk -F. -v OFS=. '{$NF++;print}' ''')
              break
            }

            if (ref.contains('refs/tags/')) {
              tag = ref.replace('refs/tags/', '')
              //TODO regex
              IMAGE_VERSION = tag
            }

          }
        }
      }

      stage('Build') {
        when {
          expression {
            SKIP.toBoolean() == false
          }
        }
        steps {
          container('docker') {
            sh label: 'build', script: 'docker build . -t ' + GCR_DOMAIN + '/' + GCP_PROJECT + '/' + IMAGE_NAME + ':' + IMAGE_VERSION
          }
        }
      }
      stage('Publish') {
        when {
          expression {
            SKIP.toBoolean() == false
          }
        }
        steps {
          container('docker') {
            withCredentials([file(credentialsId: 'gcr', variable: 'json_key')]) {
                sh label: 'docker login', script: 'cat $json_key | docker login -u _json_key --password-stdin ' + GCR_DOMAIN
            }
            sh label: 'docker push', script: 'docker push ' + GCR_DOMAIN + '/' + GCP_PROJECT + '/' + IMAGE_NAME + ':' + IMAGE_VERSION
          }
        }
      }

      stage('Git tag') {
        when {
          expression {
            "$ref" == 'main' || "$ref" == 'release'
          }
        }
        steps {
          sh label: 'git add tag', script: 'git tag ' + IMAGE_VERSION
          sh label: 'git push tag', script: 'git push origin ' + IMAGE_VERSION
        }
      }

    }
}

