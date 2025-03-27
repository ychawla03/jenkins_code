#!/usr/bin/env groovy

void setBuildStatus(String message, String state) {
  step([
      $class: "GitHubCommitStatusSetter",
      reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/ychawla03/jenkins_code.git"],
      contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
      errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
      statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
  ]);
}

//MAVEN_ENV needs to be set in Jenkins - Manage jenkins - Tools - under maven - key should be
//there should be MAVEN_ENV
pipeline {
  agent any

  environment {
    MAVEN_ARGS=" -B -e -U"
  }

  stages {

        stage('Build') {
            steps {
                withMaven(maven: 'MAVEN_ENV') {
                    bat "mvn clean install -DskipTests=true -Dmaven.javadoc.skip=true -Dcheckstyle.skip=true ${MAVEN_ARGS}"
                }
            }
        }

        stage('Unit tests') {
            steps {
                withMaven(maven: 'MAVEN_ENV') {
                    bat "mvn clean test-compile surefire:test ${MAVEN_ARGS}"
                }
            }
        }

        stage('Integration tests') {
           steps {
                withMaven(maven: 'MAVEN_ENV') {
                    bat "mvn clean verify -Dskip.unit.tests=true ${MAVEN_ARGS}"
                }
           }
          post {
              success {
                        junit allowEmptyResults: true, testResults: '**/*-reports/*.xml'
                    }
              }
        }

        stage('Code quality - sonar') {
            steps {
                bat """
                echo "Running sonar Analysis"
                """
			//	withMaven(maven: 'MAVEN_ENV') {
            //      sh "mvn sonar:sonar -Dsonar.projectKey=${SONAR_PROJECT} -Dsonar.projectName=${SONAR_PROJECT} -Dsonar.host.url=${SONAR_URL} -Dsonar.analysis.trigrame=${TRIGRAMME} -Dsonar.analysis.version=${VERSION} -Dsonar.analysis.itbweb=TRUE -Dsonar.login=${SONAR_LOGIN}"

             //   }
            }
        }

        stage('OWASP Dependency-Check Vulnerabilities') {
            steps {
				bat """
                echo "OWASP Dependency-Check Vulnerabilities"
                """
            }
        }
 }

    post {
        success {
            setBuildStatus("Build succeeded", "SUCCESS");
        }
        failure {
            setBuildStatus("Build failed", "FAILURE");
        }
    }	
}  
