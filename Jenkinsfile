pipeline {
	agent any

	environment {
		KGX_GIT='https://github.com/NCATS-tangerine/kgx.git'
		PYTHONPATH='$WORKSPACE/kgx'
		NEO4J_PASS = credentials('scigraph_pass')
	}
	options {
		// using the timestamps plugin we can add timestamps to the console log
		timestamps()
	}

	stages {
		stage('KGX checkout') {
			steps {
				sh "cd $WORKSPACE"
				sh "pip3.7 install git+https://github.com/NCATS-Tangerine/kgx@validate"
				script {
					if (!fileExists('$WORKSPACE/data')) {
						sh "mkdir -p $WORKSPACE/data"
					}
					if (!fileExists('$WORKSPACE/results')) {
						sh "mkdir -p $WORKSPACE/results"
					}
				}
			}
		}
		stage('Last stage') {
			steps {
				sh "python --version"
				sh "kgx --version"
				sh "which python"
				sh "which kgx"
				sh "kgx validate db.csv.tar -o test"
			}
		}
		stage('Data download') {
			steps {
				sh "ls db.csv.tar || kgx neo4j-download -a http://scigraph.ncats.io -u neo4j -p ${env.NEO4J_PASS} -o db.csv"
			}
		}
		stage('Last stage') {
			steps {
				sh "kgx validate db.csv.tar -o test"
			}
		}
	}

	post {
		always {
			// archive contents in results folder, only if the build is successful
			archiveArtifacts artifacts: 'test/*', onlyIfSuccessful: true

			// delete all created directories
			//deleteDir()

			// clean workspace
			//cleanWs()
		}
	}
}
