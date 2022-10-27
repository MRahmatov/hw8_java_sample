pipeline {
    agent any
    triggers {
        cron('0 5 * * *')
    }
    parameters {
        string(name: 'TAG', defaultValue: "latest")
        booleanParam(name: 'SKIP_TEST', defaultValue: false)
        booleanParam(name: 'SKIP_PUBLISH_IMAGE', defaultValue: false)
    }
    tools {
        maven 'Maven_3.8.6'
        git 'Default'
    }
    
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Test') {
            when {
                expression {
                    return params.SKIP_TEST == false;
                }
            }
            steps {
                sh 'mvn clean test'
            }
        }
        stage('Build image') {
            steps {
              sh ('docker build -t java-sample:${TAG} .')
            }
        }
        stage('Publish image') {
            when {
                expression {
                    return params.SKIP_PUBLISH_IMAGE == false;
                }
            }
            steps{
               withCredentials([usernamePassword(credentialsId: 'userTestID', passwordVariable: 'userpass', usernameVariable: 'userkey')]) {
                    sh('docker login -u ${dockerHubUser} -p ${dockerHubPassword}')
                    sh('docker push mrahmatov/hw8_java_sample:${TAG}')
                }
            }
        }
    }
}
