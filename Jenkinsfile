pipeline {
    agent none
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }
        stage('Test') { 
            agent {
                docker {
                    image 'qnib/pytest' 
                }
            }
            steps {
                sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py' 
            }
            post {
                always {
                    junit 'test-reports/results.xml' 
                }
            }
        }
        stage("Manual Approval") {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                input message: 'Lanjutkan ke tahap Deploy?'
            }
        }
        stage("Deploy") {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                sh './jenkins/scripts/deliver.sh'
                sleep 60
                sh './jenkins/scripts/kill.sh' 
            }
        }
    }
}