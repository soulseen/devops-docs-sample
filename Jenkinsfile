pipeline {
  agent {
    node {
      label 'nodejs'
    }
  }
  parameters{
     string(name:'TAG_NAME',defaultValue: '',description:'')
  }
  environment {
    DOCKERHUB_CREDENTIAL_ID = 'dockerhub-id'
    GITHUB_CREDENTIAL_ID = 'github-id'
    KUBECONFIG_CREDENTIAL_ID = 'demo-kubeconfig'
    DOCKERHUB_NAMESPACE = 'zhuxiaoyang'
    GTIHUB_ACCOUNT = 'soulseen'
    APP_NAME = 'devops-docs-sample'
  }
  stages {
    stage('checkout scm') {
      steps {
        checkout(scm)
      }
    }
    stage('get dependencies') {
      steps {
        container('nodejs') {
          sh 'npm install -g cnpm --registry=https://registry.npm.taobao.org'
          sh 'cnpm i --no-package-lock'
        }

      }
    }

    // stage('unit test') {
    //   steps {
    //     container('nodejs') {
    //       sh 'yarn test'
    //     }
    //   }
    // }

    stage('build & push snapshot') {
      steps {
        container('nodejs') {
          sh 'yarn build'
          sh 'docker build -t docker.io/$DOCKERHUB_NAMESPACE/$APP_NAME:SNAPSHOT-$BRANCH_NAME-$BUILD_NUMBER .'
          withCredentials([usernamePassword(passwordVariable : 'DOCKER_PASSWORD' ,usernameVariable : 'DOCKER_USERNAME' ,credentialsId : "$DOCKERHUB_CREDENTIAL_ID" ,)]) {
            sh 'echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin'
            sh 'docker push  docker.io/$DOCKERHUB_NAMESPACE/$APP_NAME:SNAPSHOT-$BRANCH_NAME-$BUILD_NUMBER '
          }
        }

      }
    }

  }

}