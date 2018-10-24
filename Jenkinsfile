#!/usr/bin/env groovy
/**
        * ReqA Class description
 */

node("docker-jnpl2") {
    stage("GIT-PULL"){
        git 'https://github.com/spring-projects/spring-petclinic.git'
    }
    stage("CLEAN COMPILE"){
        withMaven(maven: 'maven') {
        // Run the maven clean compile
        sh "mvn clean compile"
        }
    }
    stage("SONAR"){
        def scannerHome = tool 'sonar';
        def projectName = 'pet-clinic';
        def projectKey = 'pet';
        def binaries = 'target/classes';
        def javaVersion = '1.8';
        def sourcesJava = 'src/main/java';
        def projectBaseDir = '/home/jenkins/agent/workspace/pet-clinic';
        
        withSonarQubeEnv('sonar') {
            sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectName=${projectName} -Dsonar.projectKey=${projectKey} -Dsonar.java.binaries=${binaries} -Dsonar.sources=${sourcesJava} -Dsonar.java.source=${javaVersion} -Dsonar.projectBaseDir=${projectBaseDir}"
        }
    }
    stage('PACKAGE'){
        withMaven(maven: 'maven') {
        // Run the maven package
        sh "mvn package"
        }
    }
    stage('PUSH TO ARTIFACTORY'){
        def server = Artifactory.server '882b373c80e2'
        def uploadSpec = """{
          "files": [
            {
              "pattern": "target/*.jar",
              "target": "ext-release-local/pet-files/"
            }
         ]
        }"""
        def downloadSpec = """{
             "files": [
              {
                  "pattern": "ext-release-local/pet-files/*.jar",
                  "target": "target/"
                }
             ]
            }"""
        def buildInfo2 = server.upload uploadSpec
        def buildInfo1 = server.download downloadSpec
        buildInfo1.append buildInfo2
        server.publishBuildInfo buildInfo1
    }
}