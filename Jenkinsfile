pipeline {
  agent {
    kubernetes {
      yamlFile 'build-agent.yaml'
      defaultContainer 'maven'
      idleMinutes 1
    }

  }
  stages {
    stage('Build') {
      parallel {
        stage('Compile') {
          steps {
            container(name: 'maven') {
              sh 'mvn compile'
            }

          }
        }

      }
    }

    stage('Test') {
      parallel {
        stage('Unit Tests') {
          steps {
            container(name: 'maven') {
              sh 'mvn test'
            }
          }
        }
        stage('OSS License Checker') {
          steps {
            container('licensefinder') {
              sh 'ls -al'
              sh '''#!/bin/bash --login
              /bin/bash --login
              rvm use default
              gem install license_finder
              license_finder
              '''
            }
          }
        }
      }
    }

    stage('Package') {
      parallel {
        stage('Create Jarfile') {
          steps {
            container(name: 'maven') {
              sh 'mvn package -DskipTests'
            }
          }
        }

        stage('Docker BnP') {
          steps {
            container(name: 'kaniko') {
              sh '''/kaniko/executor --verbosity debug -f `pwd`/Dockerfile -c `pwd` --insecure --skip-tls-verify --cache=true --destination=docker.io/tzuhsuannn/dso-demo:latest '''
            }
          }
        }

      }
    }

    stage('Deploy to Dev') {
      steps {
        sh 'echo done'
      }
    }
  }
}
