#!/usr/bin/env groovy

node {
    stage('checkout') {
        checkout scm
    }

    stage('check java') {
        bat "java -version"
    }

    stage('clean') {
        bat "attrib +x mvn"
        bat "./mvn clean"
    }

    stage('install tools') {
        bat "./mvnw com.github.eirslett:frontend-maven-plugin:install-node-and-npm -DnodeVersion=v10.15.0 -DnpmVersion=6.4.1"
    }

    stage('npm install') {
        bat "./mvnw com.github.eirslett:frontend-maven-plugin:npm"
    }

    stage('backend tests') {
        try {
            bat "./mvnw test"
        } catch(err) {
            throw err
        } finally {
            junit '**/target/surefire-reports/TEST-*.xml'
        }
    }

    stage('frontend tests') {
        try {
            bat "./mvnw com.github.eirslett:frontend-maven-plugin:npm -Dfrontend.npm.arguments='run test'"
        } catch(err) {
            throw err
        } finally {
            junit '**/target/test-results/TESTS-*.xml'
        }
    }

    stage('package and deploy') {
        bat "./mvnw com.heroku.sdk:heroku-maven-plugin:2.0.5:deploy -DskipTests -Pprod -Dheroku.buildpacks=heroku/jvm -Dheroku.appName=store"
        archiveArtifacts artifacts: '**/target/*.war', fingerprint: true
    }
}
