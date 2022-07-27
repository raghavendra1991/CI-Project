pipeline {
    agent any
    environment {   
        http_proxy = 'http://127.0.0.1:3128/'
        https_proxy = 'http://127.0.0.1:3128/'
        ftp_proxy = 'http://127.0.0.1:3128/'
        socks_proxy = 'socks://127.0.0.1:3128/'     
    }
    stages {
        stage ('Git checkout') {
            steps {
                // Clean before build
                cleanWs()
                git branch: 'master', credentialsId: 'github', url: 'https://github.com/gopi732/sample-ci-python.git'
            }
        }
        stage ('Install dependencies and application') {
            steps {
                sh 'pip install -r requirements.txt && pip install -e .'
            }
        }
        stage ('Test') {
            steps {
                sh 'python3 -m pytest'       
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
    		        sh 'jf rt upload test-reports/ DevOps-CI/'
				}
            }
	    }
    }
}
