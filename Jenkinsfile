#!/usr/bin/groovy

@Library(['github.com/indigo-dc/jenkins-pipeline-library@release/1.4.0']) _

def job_result_url = ''

pipeline {
    agent {
        label 'python3.6'
    }

    environment {
        author_name = "Fernando Aguilar"
        author_email = "aguilarf@ifca.unican.es"
        app_name = "xdc_lfw_integration_tests"
        job_location = "TODO"
        job_location_test = "TODO"
    }

    stages {
        stage('Code fetching') {
            steps {
                checkout scm
            }
        }
        stage('Component integration tests') {
            steps {
                echo "Running tests in a fully containerized environment..."
                dir ('.') {
                    sh '''apt-get update
apt-get install -y docker.io
curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
docker-compose up
'''
                }
            }
        }
    }

    post {
        failure {
            script {
                currentBuild.result = 'FAILURE'
            }
        }

        always  {
            script { //stage("Email notification")
                def build_status =  currentBuild.result
                build_status =  build_status ?: 'SUCCESS'
                def subject = """
New ${app_name} build in Jenkins@DEEP:\
${build_status}: Job '${env.JOB_NAME}\
[${env.BUILD_NUMBER}]'"""

                def body = """
Dear ${author_name},\n\n
A new build of '${app_name} LifeWatch XDC-Data module:\n\n
*  ${env.BUILD_URL}\n\n
terminated with '${build_status}' status.\n\n
Check console output at:\n\n
*  ${env.BUILD_URL}/console\n\n
and resultant Docker image rebuilding job at (may be empty in case of FAILURE):\n\n
*  ${job_result_url}\n\n
DEEP-XDC Jenkins CI service"""

                EmailSend(subject, body, "${author_email}")
            }
        }
    }
}
