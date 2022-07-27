pipeline {
    agent any
    environment {   
        http_proxy = 'http://127.0.0.1:3128/'
        https_proxy = 'http://127.0.0.1:3128/'
        ftp_proxy = 'http://127.0.0.1:3128/'
        socks_proxy = 'socks://127.0.0.1:3128/'     
    }
    stages {
        stage ('Clean Up WorkSpace') {
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
        stage ('Test') {
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
                archiveArtifacts artifacts: 'coverage/'
            }
        }
        stage ('Publish Artifactory') {
            steps {
		withCredentials([usernamePassword(credentialsId: 'artifactory', passwordVariable: 'passwd', usernameVariable: 'user')]) {
    		   sh 'jf rt upload tcoverage/ python-app/'
		}
            }
	}
    }
}
