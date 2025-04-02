pipeline {
    agent {
        docker {
            image 'python:3.11-bullseye'  // Use bullseye for apt support
            args '-v "$PWD":/app -w /app'  // Mount workspace & set working directory
        }
    }
    
    options {
        skipStagesAfterUnstable()
    }
    
    stages {
        stage('Setup') {
            steps {
                sh '''
                    apt-get update && apt-get install -y binutils
                    python -m pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }
        
        stage('Build') {
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                stash name: 'sources', includes: 'sources/**'
            }
        }
        
        stage('Test') {
            steps {
                sh 'pytest --junit-xml test-reports/results.xml sources/test_calc.py'
                junit 'test-reports/results.xml'
            }
        }
        
        stage('Deliver') {
            steps {
                sh "pyinstaller --onefile sources/add2vals.py"
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
                }
            }
        }
    }
}
