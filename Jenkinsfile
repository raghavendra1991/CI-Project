pipeline {
    agent any
    options {
        // This is required if you want to clean before build
        skipDefaultCheckout(true)
    }
    stages {
        stage('Build') {
            steps {
                // Clean before build
                cleanWs()
                // We need to explicitly checkout from SCM here
                checkout scm
                echo "Building ${env.JOB_NAME}..."
            }
        }
        stage ('Install dependencies') {
            steps {
                sh 'pip install -r requirements.txt && pip install -e .'
            }
        }
        stage ('Test & SonarQube Analysis') {
	    environment {
		scannerHome = tool 'SonarQube Scanner'
	    }
            steps {
                sh 'python3 -m pytest'
		withSonarQubeEnv('admin') {
		   sh '${scannerHome}/bin/sonar-scanner \
		   -D sonar.projectKey=python-app'
		}
            }
        }
        stage ('Archive artifacts') {
            steps {
                archiveArtifacts artifacts: 'htmlcov/'
            }
        }
        stage ('Publish Artifactory') {
            steps {
		withCredentials([usernamePassword(credentialsId: 'artifactory', passwordVariable: 'passwd', usernameVariable: 'user')]) {
    		   sh 'jf rt upload htmlcov/ python-app/'
		}
            }
	}
    }
}
