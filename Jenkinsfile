pipeline {
    agent {
        kubernetes {
            defaultContainer 'jnlp'
            yaml """
                apiVersion: v1
                kind: Pod
                spec:
                  containers:
                  - name: newman
                    image: postman/newman:6-alpine
                    command:
                    - cat
                    tty: true
            """
        }
    }

    options {
        timestamps()
    }

    environment {
        // These variables are mandatory to stay exactly how they are, or else
        // it won't be able to identify the moment to build or promote an image
        GIT_COMMIT_SHORT = sh (
            script: 'git rev-parse --short=8 ${GIT_COMMIT}',
            returnStdout: true
        ).trim()
    }

    stages {
        stage('Postman Test') {
            steps {
                script {
                    try {
                        container('newman') {
                            sh 'newman run HelloWorldCollection.json'
                        }
                    } catch (Exception e) {
                        echo "Postman tests failed but continuing with pipeline execution"
                    }
                }
            }
        }
    }

}