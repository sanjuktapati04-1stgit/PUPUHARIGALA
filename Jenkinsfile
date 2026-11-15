pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    parameters {
        string(
            name: 'TOMCAT_CONTAINER',
            defaultValue: 'jenkins_tomcat1',
            description: 'Name of the running Podman Tomcat container'
        )
        string(
            name: 'TOMCAT_WEB_ROOT',
            defaultValue: '/usr/local/tomcat/webapps/ROOT',
            description: 'Tomcat web root in the container (set to the mounted mnt path if different)'
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
