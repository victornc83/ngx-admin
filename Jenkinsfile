// Instead of annotating an unnecessary import statement, the symbol _ is annotated, according to the annotation pattern.
@Library('adop-pluggable-scm-jenkinsfile') _

def repoName = "ngx-admin"
def regRepo = "adop-cartridge-npm-regression-tests"

pipeline {
    agent { label 'docker' }
    tools {nodejs "ADOP NodeJS 8"}
    environment{
        ENVIRONMENT_NAME = 'CI'
    }
    stages{
        stage("Reference Application Build"){
            steps{
                echo 'This is a reference  Application pipeline.'
                deleteDir()
                checkout scmGet("${SCM_URL}", "${SCM_NAMESPACE}", "${repoName}", "${SCM_CREDENTIAL_ID}", 'master')
                sh '''
                    set -x
                    npm install
                    npm run build:prod
                '''
            }
        }
        stage("Reference Application Unit Tests"){
            steps{
                echo 'This job runs unit tests on Java Spring reference application.'
                echo 'npm run test'
            }  
        }
        stage("Reference Application Code Analysis"){
            steps{
                echo 'This job runs code quality analysis for Java reference application using SonarQube.'
                echo "Checking the Build number $BUILD_NUMBER"
                script{
                    // requires SonarQube Scanner 2.8+
                    scannerHome = tool 'ADOP SonarScanner'
                }
            }
        }
        stage("Reference Application Deploy"){
            steps{
                echo 'This job deploys the java reference application to the CI environment.'
                sh '''
                    set -x
                    export SERVICE_NAME="$(echo ${PROJECT_NAME} | tr \'/\' \'_\')_${ENVIRONMENT_NAME}"
                    docker cp ${WORKSPACE}/dist/*  ${SERVICE_NAME}:/usr/share/nginx/html/
                    docker restart ${SERVICE_NAME}
                    COUNT=1
                    echo "Count : ${COUNT}"
                    while ! curl -q http://${SERVICE_NAME} -o /dev/null
                        do
                            if [ ${COUNT} -gt 10 ]; then
                                echo "Docker build failed even after ${COUNT}. Please investigate."
                                exit 1
                            fi
                            echo "Application is not up yet. Retrying ..Attempt (${COUNT})"
                            sleep 5
                            COUNT=$((COUNT+1))
                        done
                    echo "=.=.=.=.=.=.=.=.=.=.=.=."
                    echo "=.=.=.=.=.=.=.=.=.=.=.=."
                    echo "Environment URL (replace PUBLIC_IP with your public ip address where you access jenkins from) : http://${SERVICE_NAME}.PUBLIC_IP.xip.io/ngxadmin"
                    echo "=.=.=.=.=.=.=.=.=.=.=.=."
                    echo "=.=.=.=.=.=.=.=.=.=.=.=."
                    set -x
                '''
                stash includes: '**/**', name: 'build-artefacts'
            }
        }
        stage("Reference Application Regression Tests"){
            steps{
                echo 'This job runs regression tests on deployed java application.'
            }
        }
        stage("Reference Application Performance Tests"){
            steps{
                echo 'This job run the Jmeter test for the java reference application.'
            }
        }
        stage("Reference Application Deploy ProdA"){
            steps{
                echo 'This job deploys the java reference application to the ProdA environment'
                timeout(time:5, unit:'MINUTES') {
                    input('! Deploy to Prod A Environment?')
                }
                sh '''
                    set -x
                    export SERVICE_NAME="$(echo ${PROJECT_NAME} | tr \'/\' \'_\')_PRODA"
                    docker cp ${WORKSPACE}/dist/*  ${SERVICE_NAME}:/usr/share/nginx/html/
                    docker restart ${SERVICE_NAME}
                    COUNT=1
                    echo "Count : ${COUNT}"
                    while ! curl -q http://${SERVICE_NAME} -o /dev/null
                        do
                            if [ ${COUNT} -gt 10 ]; then
                                echo "Docker build failed even after ${COUNT}. Please investigate."
                                exit 1
                            fi
                            echo "Application is not up yet. Retrying ..Attempt (${COUNT})"
                            sleep 5
                            COUNT=$((COUNT+1))
                        done
                    echo "=.=.=.=.=.=.=.=.=.=.=.=."
                    echo "=.=.=.=.=.=.=.=.=.=.=.=."
                    echo "Environment URL (replace PUBLIC_IP with your public ip address where you access jenkins from) : http://${SERVICE_NAME}.PUBLIC_IP.xip.io/ngxadmin"
                    echo "=.=.=.=.=.=.=.=.=.=.=.=."
                    echo "=.=.=.=.=.=.=.=.=.=.=.=."
                    set -x
                '''
            }
        }
        stage("Reference Application Deploy ProdB"){
            steps{
                echo 'This job deploys the java reference application to the ProdB environment'
                timeout(time:5, unit:'MINUTES') {
                    input('! Deploy to Prod B Environment?')
                }
                sh '''
                    set -x
                    export SERVICE_NAME="$(echo ${PROJECT_NAME} | tr \'/\' \'_\')_PRODB"
                    docker cp ${WORKSPACE}/dist/*  ${SERVICE_NAME}:/usr/share/nginx/html/
                    docker restart ${SERVICE_NAME}
                    COUNT=1
                    echo "Count : ${COUNT}"
                    while ! curl -q http://${SERVICE_NAME} -o /dev/null
                        do
                            if [ ${COUNT} -gt 10 ]; then
                                echo "Docker build failed even after ${COUNT}. Please investigate."
                                exit 1
                            fi
                            echo "Application is not up yet. Retrying ..Attempt (${COUNT})"
                            sleep 5
                            COUNT=$((COUNT+1))
                        done
                    echo "=.=.=.=.=.=.=.=.=.=.=.=."
                    echo "=.=.=.=.=.=.=.=.=.=.=.=."
                    echo "Environment URL (replace PUBLIC_IP with your public ip address where you access jenkins from) : http://${SERVICE_NAME}.PUBLIC_IP.xip.io/ngxadmin"
                    echo "=.=.=.=.=.=.=.=.=.=.=.=."
                    echo "=.=.=.=.=.=.=.=.=.=.=.=."
                    set -x
                '''
            }
        }   
    }
}