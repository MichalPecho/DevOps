pipeline {
  agent any
  stages {
    stage('Checkout SCM') {
      steps {
        checkout([$class: 'GitSCM', branches: [[name: '${BRANCH}']], userRemoteConfigs: [[url: '${REPO}']]])
      }
    }

    stage('Package the gems') {
      steps {
        sh '''#!/bin/bash -le
                BRANCH=$BRANCH make config
                '''
      }
    }

    stage('Build the container image') {
      steps {
        sh '''#!/bin/bash -le
                NAMESPACE=$NAMESPACE BRANCH=$BRANCH make build
                '''
      }
    }

    stage('Push the container image to the Docker registry') {
      steps {
        script {
          sh '''#!/bin/bash -le
NAMESPACE=$NAMESPACE BRANCH=$BRANCH make push
'''
        }

      }
    }

  }
  post {
    always {
      sh '''#!/bin/bash -le
            NAMESPACE=$NAMESPACE BRANCH=$BRANCH make clean
            '''
      deleteDir()
    }

  }
  parameters {
    string(name: 'REPO', defaultValue: 'git@github.com:MichalPecho/DevOps.git', description: 'The git repository')
    string(name: 'BRANCH', defaultValue: 'master', description: 'Branch to build')
    string(name: 'NAMESPACE', defaultValue: 'MichalPecho', description: 'Namespace for the Docker Image')
    string(name: 'REGISTRY', defaultValue: 'index.docker.io', description: 'Registry to be used')
  }
}