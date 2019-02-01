#!/usr/bin/env groovy
/*
 * Copyright (c) 2018 Questem Co., Ltd.
 */

pipeline {
	agent any
	tools {
		maven 'Maven 3.3.9'
	}
	stages {
		stage('Initialize') {
			steps {
				echo 'Initialize...'
				sh 'printenv'
				sh 'mvn clean initialize --batch-mode --show-version'
			}
		}
		stage('Build Pull Request') {
			when {
				changeRequest()
			}
			steps {
				echo 'Build & Verify...'
				sh 'mvn verify --batch-mode'
			}
			post {
				always {
					junit allowEmptyResults:true, testResults:'**/target/surefire-reports/TEST-*.xml'
					junit allowEmptyResults:true, testResults:'**/target/failsafe-reports/TEST-*.xml'
					checkstyle canRunOnFailed:true, pattern:'**/target/checkstyle-result.xml'
				}
			}
		}
		stage('Deploy') {
			when {
				branch 'master'
			}
			steps {
				echo 'Deploy...'
				sh 'mvn deploy -Dmaven.test.skip=true -Dmaven.install.skip=true --batch-mode'
			}
		}
	}
	post {
		success {
			sh "mvn clean --batch-mode"
			deleteDir()
		}
	}
}
