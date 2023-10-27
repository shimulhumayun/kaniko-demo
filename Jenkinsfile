pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        namespace: jenkins-n
        metadata:
          labels:
            app: test
        spec:
          containers:
          - name: git
            image: bitnami/git:latest
            command:
            - cat
            tty: true
          - name: maven
            image: maven:3.8.3-adoptopenjdk-11
            command:
            - cat
            tty: true
          - name: kaniko
            image: gcr.io/kaniko-project/executor:debug
            command:
            - cat
            tty: true
            volumeMounts:
            - name: kaniko-secret
              mountPath: /kaniko/.docker
          volumes:
          - name: kaniko-secret
            secret:
              secretName: regcred
              items:
                - key: .dockerconfigjson
                  path: config.json
      '''
    }      
  }
  environment{
    DOCKERHUB_USERNAME = "shimuul921"
    APP_NAME = "kaniko-webapp-demo-jenkins"
    IMAGE_NAME = "${DOCKERHUB_USERNAME}" + "/" + "${APP_NAME}"
    IMAGE_TAG = "${BUILD_NUMBER}"
  }
    stage('Build SW'){
      steps {
        container('maven'){
          sh 'mvn -Dmaven.test.failure.ignore=true clean package'
        }
      }
    }
    stage('Build Container Image'){
      steps {
        container('kaniko'){
          sh "/kaniko/executor --context $WORKSPACE --destination $IMAGE_NAME:$IMAGE_TAG"
        }
      }
    }
  }

