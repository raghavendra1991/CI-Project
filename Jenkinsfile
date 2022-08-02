pipeline {
    agent any
    stages {
        stage('CleanUp WorkSpace') {
            steps {
                // Clean before build
                cleanWs()
                // We need to explicitly checkout from SCM here
                checkout scm
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
		rtUpload (
		   serverId: 'JFrog',
		   spec: '''{
 			  "files" :[
			    {
		               "pattern": "htmlcov/",
		               "target": "python-app/",
	                       "recursive": "false"
			    }
		          ]
		    }'''
	        )
	    }
        }
    }
    post {
        // Clean after build
        always {
            cleanWs(cleanWhenNotBuilt: false,
                    deleteDirs: true,
                    disableDeferredWipeout: true,
                    notFailBuild: true
	    )
        }
    }
}
