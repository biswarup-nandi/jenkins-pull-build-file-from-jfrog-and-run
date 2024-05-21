pipeline {
    agent any

    environment {
        ARTIFACTORY_URL = 'https://biswarupnandi.jfrog.io/artifactory'
        ARTIFACTORY_REPO = 'dbx-dbx-python'
        ARTIFACTORY_SERVER = 'jfrog-artifact-instance'
        PYTHON_VERSION = '3.10.12'
    }

    stages {
        stage('Setup Python') {
            steps {
                sh """
                    /usr/bin/python3 --version
                    /usr/bin/python3 -m venv venv
                    . venv/bin/activate
                    python --version
                    pip --version
                    pip install --upgrade pip
                """
            }
        }

        stage('Pull and Install Wheel') {
            steps {
                script {
                    def server = Artifactory.server("${ARTIFACTORY_SERVER}")
                    def downloadSpec = """{
                        "files": [{
                            "pattern": "${ARTIFACTORY_REPO}/*.whl",
                            "target": "dist/"
                        }]
                    }"""
                    server.download(downloadSpec)
                }
                sh """
                    . venv/bin/activate
                    ls -ltr dist/
                    pip install dist/*.whl
                    pip list
                """
            }
        }

        stage('Run Main Function') {
            steps {
                sh """
                    . venv/bin/activate
                    pip list
                    dbx-utility
                """
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
