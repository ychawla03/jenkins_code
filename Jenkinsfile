#!/usr/bin/env groovy

void setBuildStatus(String message, String state) {
  step([
      $class: "GitHubCommitStatusSetter",
      reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/anicetkeric/spring-jenkins-multibranch"],
      contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
      errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
      statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
  ]);
}

pipeline {
  agent any

  environment {
    MAVEN_ARGS=" -B -e -U"
  }

  stages {

        stage('Build') {
            steps {
                withMaven(maven: 'MAVEN_ENV') {
                    sh "mvn clean install -DskipTests=true -Dmaven.javadoc.skip=true -Dcheckstyle.skip=true ${MAVEN_ARGS}"
                }
            }
        }

        stage('Unit tests') {
            steps {
                withMaven(maven: 'MAVEN_ENV') {
                    sh "mvn clean test-compile surefire:test ${MAVEN_ARGS}"
                }
            }
        }

        stage('Integration tests') {
           steps {
                withMaven(maven: 'MAVEN_ENV') {
                    sh "mvn clean verify -Dskip.unit.tests=true ${MAVEN_ARGS}"
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
                sh """
                echo "Running sonar Analysis"
                """
			//	withMaven(maven: 'MAVEN_ENV') {
            //      sh "mvn sonar:sonar -Dsonar.projectKey=${SONAR_PROJECT} -Dsonar.projectName=${SONAR_PROJECT} -Dsonar.host.url=${SONAR_URL} -Dsonar.analysis.trigrame=${TRIGRAMME} -Dsonar.analysis.version=${VERSION} -Dsonar.analysis.itbweb=TRUE -Dsonar.login=${SONAR_LOGIN}"

             //   }
            }
        }

        stage('OWASP Dependency-Check Vulnerabilities') {
            steps {
				sh """
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
