pipeline {
	agent { label 'agent1'}
	tools {
   		 maven "maven3"
        }
	stages {
	    stage('Fetch code') {
            steps {
               git branch: 'main', url: 'https://github.com/Cuongdx77/my-project-test.git'
            }
	    }

	    stage('Build'){
	        steps{
	           sh 'mvn install -DskipTests'
	        }

	        post {
	           success {
	              echo 'Now Archiving it...'
	              archiveArtifacts artifacts: '**/target/*.war'
	           }
	        }
	    }

	    stage('UNIT TEST') {
            steps{
                sh 'mvn test'
            }
       }

       stage('Checkstyle Analysis'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
       }

       stage('Sonar Analysis') {
            environment {
                scannerHome = tool 'sonarqube_scanner'
		sonarToken = credentials('Sonarqube_Cred')
            }
            steps {
               withSonarQubeEnv('Sonarqube server connection') {
                   sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=my-project \
                   -Dsonar.projectName=my-project \
		   -Dsonar.token=${sonarToken} \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
              }
           }
       }
       stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true, credentialsId: 'Sonarqube_Cred'
                }
            }
        }
	}
}
