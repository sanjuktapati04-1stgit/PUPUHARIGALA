pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    environment {
        TOMCAT_CONTAINER = 'jenkins_tomcat1'
        TOMCAT_WEB_ROOT = '/usr/local/tomcat/webapps/ROOT'
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
                sh '''
                    set -eu
                    test -n "$TOMCAT_CONTAINER"
                    test -n "$TOMCAT_WEB_ROOT"
                    podman container exists "$TOMCAT_CONTAINER"
                    podman exec "$TOMCAT_CONTAINER" mkdir -p "$TOMCAT_WEB_ROOT"
                    podman cp build/extracted/. "$TOMCAT_CONTAINER:$TOMCAT_WEB_ROOT/"
                '''
            }
        }
    }
}
