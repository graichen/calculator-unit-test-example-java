#!/usr/bin/env groovy

pipeline {
    agent any

    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    environment {
        HTTP_PROXY="${HTTP_PROXY}"
        HTTPS_PROXY="${HTTPS_PROXY}"
        NO_PROXY="${NO_PROXY}"
        SONAR_HOST_URL="https://sonarcloud.io"
        SONAR_LOGIN_KEY = credentials('SONAR_LOGIN_KEY')
    }

    stages {

        stage('Check env') {
            steps {
                sh 'id'
                sh 'env'
            }
        }

        stage('Build') {
            agent {
                docker {
                    // to run as non-root:
                    // $ docker run -v ~/.m2:/var/maven/.m2 -ti --rm -u 1000 -e MAVEN_CONFIG=/var/maven/.m2 maven mvn -Duser.home=/var/maven archetype:generate
                    image 'maven:3.6.3-jdk-8'
                    //args '-u ${JENKINS_UID} -e MAVEN_OPTS=${MAVEN_OPTS} -v ${JENKINS_HOST_HOME}:/var/jenkins -v ${MAVEN_REPO_DIR}:/var/jenkins/.m2'
                    // for windows let's skip setting user
                    //args '-u root:root -e MAVEN_OPTS=${MAVEN_OPTS} -v ${JENKINS_HOST_HOME}:/var/jenkins -v ${MAVEN_REPO_DIR}:/var/jenkins/.m2'
                    args '-u root:root -v ${JENKINS_HOST_HOME}:/var/jenkins -v ${MAVEN_REPO_DIR}:/var/jenkins/.m2'
                }
            }
            steps {
                sh 'id'
                sh 'mvn clean package'
            }
        }
        stage('SonarQube (Quality)') {
            agent {
                docker {
                    // to run as non-root:
                    // $ docker run -v ~/.m2:/var/maven/.m2 -ti --rm -u 1000 -e MAVEN_CONFIG=/var/maven/.m2 maven mvn -Duser.home=/var/maven archetype:generate
                    image 'maven:3.6.3-jdk-8'
                    //args '-u root:root  -e MAVEN_OPTS=${MAVEN_OPTS} -v ${JENKINS_HOST_HOME}:/var/jenkins -v ${MAVEN_REPO_DIR}:/var/jenkins/.m2 -e SONAR_HOST_URL=${SONAR_HOST_URL} -e SONAR_LOGIN_KEY=${SONAR_LOGIN_KEY}'
                    args '-u root:root -v ${JENKINS_HOST_HOME}:/var/jenkins -v ${MAVEN_REPO_DIR}:/var/jenkins/.m2 -e SONAR_HOST_URL=${SONAR_HOST_URL} -e SONAR_LOGIN_KEY=${SONAR_LOGIN_KEY}'
                }
            }
//            when {
//                branch "master"
//            }

            steps {
                echo 'mvn sonar:sonar -Dsonar.host.url=${SONAR_HOST_URL} -Dsonar.login=${SONAR_LOGIN_KEY} -Dsonar.projectKey=graichen_calculator-unit-test-example-java -Dsonar.organization=graichen'
                sh 'mvn sonar:sonar -Dsonar.host.url=${SONAR_HOST_URL} -Dsonar.login=${SONAR_LOGIN_KEY} -Dsonar.projectKey=graichen_calculator-unit-test-example-java -Dsonar.organization=graichen'
            }
        }

        stage('Reports') {
            agent {
                docker {
                    // to run as non-root:
                    // $ docker run -v ~/.m2:/var/maven/.m2 -ti --rm -u 1000 -e MAVEN_CONFIG=/var/maven/.m2 maven mvn -Duser.home=/var/maven archetype:generate
                    image 'maven:3.6.3-jdk-8'
                    //args '-u root:root  -e MAVEN_OPTS=${MAVEN_OPTS} -v ${JENKINS_HOST_HOME}:/var/jenkins -v ${MAVEN_REPO_DIR}:/var/jenkins/.m2'
                    args '-u root:root -v ${JENKINS_HOST_HOME}:/var/jenkins -v ${MAVEN_REPO_DIR}:/var/jenkins/.m2'
                }
            }
//            when {
//                branch "master"
//            }
            steps {
                sh 'mvn project-info-reports:dependencies license:add-third-party -Ddependency.locations.enabled=false'
                archiveArtifacts artifacts: '**/target/site/dependenc*.html, **/target/generated-sources/license/THIRD-PARTY.txt'
            }
        }
    }
//    post {
//        unstable {
//            mail to: "${env.CHANGE_AUTHOR_EMAIL}",
//                    subject: "Unstable Pipeline: ${currentBuild.fullDisplayName}",
//                    body: "Something is wrong with ${env.BUILD_URL}"
//        }
//        failure {
//            mail to: "${env.CHANGE_AUTHOR_EMAIL}",
//                    subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
//                    body: "Something is wrong with ${env.BUILD_URL}"
//        }
//        aborted {
//            mail to: "${env.CHANGE_AUTHOR_EMAIL}",
//                    subject: "Aborted Pipeline: ${currentBuild.fullDisplayName}",
//                    body: "Something is wrong with ${env.BUILD_URL}"
//        }
//        changed {
//            mail to: "${env.CHANGE_AUTHOR_EMAIL}",
//                    subject: "Changed Pipeline: ${currentBuild.fullDisplayName}",
//                    body: "Something is changed with ${env.BUILD_URL}"
//        }
//    }
}
