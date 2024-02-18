pipeline {
    agent { label 'ci-docker' }

    parameters {
        string(name: 'REPO',      defaultValue: 'git@github.com:MichalPecho/DevOps.git',         description: 'The git repository')
        string(name: 'BRANCH',    defaultValue: 'master',                                        description: 'Branch to build')
        string(name: 'NAMESPACE', defaultValue: 'MichalPecho',                                   description: 'Namespace for the Docker Image')
        string(name: 'REGISTRY',  defaultValue: 'index.docker.io',                               description: 'Registry to be used')
    }

    stages {
        /* Checkout the desired git branch*/
        stage('Checkout SCM') {
            steps {
                checkout scm: [$class: 'GitSCM', branches: [[name: '${BRANCH}']], userRemoteConfigs: [[url: '${REPO}']]]
            }
        }

        /* Package the gems*/
        stage('Package the gems') {
            steps {
                sh '''#!/bin/bash -le
                BRANCH=$BRANCH make config
                '''
            }
        }

        /* Build the Docker container image */
        stage('Build the container image') {
            steps {
                sh '''#!/bin/bash -le
                NAMESPACE=$NAMESPACE BRANCH=$BRANCH make build
                '''
            }
        }

        /* Push the build container image to the Docker registry */
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
            // clean up our workspace
            sh '''#!/bin/bash -le
            NAMESPACE=$NAMESPACE BRANCH=$BRANCH make clean
            '''
            deleteDir()
        }
    }
}
