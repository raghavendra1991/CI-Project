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
		echo "Unit Testing"
                sh 'python3 -m pytest'
		echo "Test Coverage"
		sh 'python3 -m coverage xml -o htmlcov/coverage.xml'
		withSonarQubeEnv('admin') {
		   sh '${scannerHome}/bin/sonar-scanner \
		   -D sonar.projectKey=python-app \
		   -D sonar.python.coverage.reportPaths=coverage.xml'	
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
		rtupload (
		   serverId: admin
		   spec: '''{
 			  "files" :[
			          {
		                    "pattern": "/htmlcov",
		    	            "target": "pythonapp/",
			            "recursive": "false"
			          }
		             ]
			}'''
	       	   }
	        )
	    }
        }
    }
}
