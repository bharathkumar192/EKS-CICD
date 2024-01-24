pipeline {
        agent any
		environment {
        VENV = 'venv'
    }
        stages { 
            stage('Build') {
                steps {
                    echo 'Start building'
                }
            }
            stage('Test') {
                steps {
                    sh "python3 -m venv ${VENV}"
                    sh ". ${VENV}/bin/activate"
		    sh '${VENV}/bin/pip install -r requirements.txt'
                }
            }
            stage('Deploy') {
                steps {
                    echo 'complete deploy'
                }
            }
        }
    }