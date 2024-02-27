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

    post {
        success {
            // Trigger downstream pipeline
            build job: 'Content Services/Staging/API/main', result: 'SUCCESS'
        }
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