pipeline {
    agent any
    stages {
        stage('Create  directory for the WEB Application') {
            steps {
                //First, drop the directory if exists
                pwsh 'rmdir -Force -r C:/proyectos/jenkins/paralelismo/deploy-web'

                //Create the directory
                pwsh 'mkdir C:/proyectos/jenkins/paralelismo/deploy-web'
            }
        }

        stage('Drop the containers') {
            failFast true
            parallel {
                stage('Drop Apache') {
                    steps {
                        echo 'droping apache...'
                        pwsh 'docker -f app-web-apache'
                    }
                }

                stage('Drop nginx') {
                    steps {
                        echo 'droping nginx...'
                        pwsh 'docker rm -f app-web-nginx'
                        pwsh 'docker rm -f app-web-apache'
                    }
                }
            }
        }
        //Creating the containers in Parallel
        stage('Create the containers in Parallel') {
            parallel {
                stage('Create the Apache container') {
                    steps {
                        echo 'Creating the Apache Container...'
                        pwsh 'docker run -dit --name app-web-apache -p 9100:80  -v C:/proyectos/jenkins/paralelismo/deploy-web:/usr/local/apache2/htdocs/ httpd'
                    }
                }

                stage('Create the Nginx container') {
                    steps {
                        echo 'Creating the Apache container...'
                        pwsh 'docker run -dit --name app-web-nginx -p 9200:80  -v C:/proyectos/jenkins/paralelismo/deploy-web:/usr/share/nginx/html nginx'
                    }
                }
            }
        }

        //Copy the application
        stage('Copy the web application to the container directory') {
            steps {
                echo 'Copying web application...'
                pwsh 'cp -r web/* C:/proyectos/jenkins/paralelismo/deploy-web'
            }
        }
    }

    post {
        success {
            // One or more steps need to be included within each condition's block.
            echo 'The deployment in Nginx and Apache has worked'
            archiveArtifacts allowEmptyArchive: true, artifacts: 'web/*', followSymlinks: false
            cleanWs()
        }

        failure {
            // One or more steps need to be included within each condition's block.
            echo 'An error has ocurred in the deploy'
        }
    }
}
