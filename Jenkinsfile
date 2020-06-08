pipeline {
    agent any
    stages {
        stage('get source') {
            steps {
                echo "build id = v2.${env.BUILD_ID}"
                echo 'downloading the source code from git'
                git branch: 'master',
                    url: 'https://github.com/Deveshdvs/internal-demo.git'
                sh 'ls -a'
                
            }
        }
        stage('check versions') {
            steps {
                sh 'docker --version'
                sh 'gcloud version'
                sh 'nodejs -v'
                sh 'npm -v'
            }
        }
        stage('npm install and test') {
            steps {
                echo 'running npm install'
                sh 'npm install'
                echo 'running tests'
                sh 'npm test'
            }
        }
        stage('build') {
            steps {
                echo "build id = v2.${env.BUILD_ID}"
                sh "gcloud builds submit --tag gcr.io/dtc-user13/internal:v2.${env.BUILD_ID} ."
            }
        }
        stage('kubernets deploy') {
            steps {
                echo "connect to cluster"
                sh 'gcloud container clusters get-credentials cluster-demo --zone asia-east1-a --project dtc-user13'
                echo "setting new image"
                sh "kubectl set image deployments/events-data events-data=gcr.io/dtc-user13/internal:v2.${env.BUILD_ID} --record"
            }
        }
    }
}