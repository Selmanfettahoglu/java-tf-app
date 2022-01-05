#!/usr/bin/env groovy

pipeline {
    agent any
    tools {
        maven 'maven-3.8'
        terraform 'tf'
    }
    environment {
        IMAGE_NAME = "selmanfettahoglu/java-maven-${BUILD_NUMBER}"
    }
    stages {
    
        /* stage('test') {
            steps {
                script {
                    echo "test the application"
                    sh 'mvn test'
                }
            }
        } */
 /*        stage('build jar') {
            steps {
                script {
                    echo "building the application jar"
                    sh 'mvn package'
                }
            }
        }
 */
        stage('build image') {
            steps {
                script {
                    echo "building the docker image"
                     withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh 'docker build -t ${IMAGE_NAME} .'
                        sh "echo $PASS | docker login -u $USER --password-stdin"
                        sh 'docker push ${IMAGE_NAME}'
                    }
                }
            }
        }
        stage('provision server') {
           steps {
                script {
                    dir('terraform') {
                        withCredentials([[$class: 'AmazonWebServicesCredentialsBinding',credentialsId: "AWS-ID",accessKeyVariable: 'AWS_ACCESS_KEY_ID',secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                            sh "terraform destroy"
                        }
                    }
                }
            }
        }
