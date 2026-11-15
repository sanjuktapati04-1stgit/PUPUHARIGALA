pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    parameters {
        string(
            name: 'DEPLOY_DIR',
            defaultValue: 'build/deployed-site',
            description: 'Directory on the Jenkins agent where the website should be deployed'
        )
    }

    stages {
        stage('Clone') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh '''
                    set -eu
                    test -f test.html
                    mkdir -p build/site
                    cp test.html build/site/index.html
                    tar -czf build/site.tar.gz -C build/site .
                '''
            }
        }

        stage('Copy') {
            steps {
                sh '''
                    set -eu
                    mkdir -p build/package
                    cp build/site.tar.gz build/package/site.tar.gz
                '''
            }
        }

        stage('Extract') {
            steps {
                sh '''
                    set -eu
                    mkdir -p build/extracted
                    tar -xzf build/package/site.tar.gz -C build/extracted
                '''
            }
        }

        stage('Deploy') {
            steps {
                script {
                    if (params.DEPLOY_DIR == null || params.DEPLOY_DIR.trim().isEmpty()) {
                        error('Set the DEPLOY_DIR build parameter to the website deployment directory.')
                    }
                }
                sh '''
                    set -eu
                    mkdir -p "$DEPLOY_DIR"
                    cp -R build/extracted/. "$DEPLOY_DIR"/
                '''
            }
        }
    }
}
