pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    environment {
        TOMCAT_WEB_ROOT = '/mnt/tomcat/webapps/ROOT'
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
                    test -n "$TOMCAT_WEB_ROOT"
                    test -d "$TOMCAT_WEB_ROOT" || {
                        echo "Tomcat shared mount is not available at $TOMCAT_WEB_ROOT" >&2
                        exit 1
                    }
                    cp -R build/extracted/. "$TOMCAT_WEB_ROOT"/
                '''
            }
        }
    }
}
