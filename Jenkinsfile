// Declarative pipeline
pipeline {
    agent any
    options {
        // This is required if you want to clean before build
        skipDefaultCheckout(true)
    }
    stages {
        stage('CleanUp WorkSpace') {
            steps {
                // Clean before build
                cleanWs()
                // We need to explicitly checkout from SCM here
                checkout scm
                echo "Building ${env.JOB_NAME}..."
            }
        }
	    stage ('Build') {
	        steps {
		        sh 'pip install -r requirements.txt'
	        }
	    }
	    stage ('test') {
	        steps {
		        sh 'python3 test.py'
			
	        }
	    }	
	    stage('SonarQube Analysis') {
	        environment {
		        scannerHome = tool 'SonarQube Scanner'
            }
	        steps {				
		        withSonarQubeEnv('admin') {
		            sh '${scannerHome}/bin/sonar-scanner \
 	   	            -D sonar.projectKey=sampledevopsproject \
		            -D sonar.python.coverage.reportPaths=coverage.xml'	
		        }
	        }
	    }
	    stage ('Archive artifacts') {
            steps {
                archiveArtifacts artifacts: 'test-reports/'
            }
        }
        stage ('Publish Artifactory') {
	        steps {
		        rtUpload (
		            serverId: 'JFrog',
		            spec: '''{
 			            "files" :[
			                {
		                         "pattern": "test-reports/",
		                        "target": "python-app/",
	                            "recursive": "false"
			                }
		                ]
		            }'''
	            )
	        }
        }
    }
}
