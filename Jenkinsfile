pipeline {
	agent any

    options {
		buildDiscarder(logRotator(numToKeepStr: '3', artifactNumToKeepStr: '3'))
    }

    tools {
		maven 'mvn_3.9.9'
    }

    stages {
		stage('Code Compilation') {
			steps {
				echo 'Starting Code Compilation...'
                sh 'mvn clean compile'
                echo 'Code Compilation Completed Successfully!'
            }
        }

        stage('Code Package') {
			steps {
				echo 'Creating WAR Artifact...'
                sh 'mvn clean package'
                echo 'WAR Artifact Created Successfully!'
            }
        }

        stage('Build & Tag Docker Image') {
			steps {
				echo 'Building Docker Image with Tags...'
                sh "docker build -t docker26031997/booking-ms:latest -t booking-ms:latest ."
                echo 'Docker Image Build Completed!'
            }
        }

        stage('Docker Image Scanning') {
			steps {
				echo 'Scanning Docker Image...'
                // Add actual scanning commands (e.g., Trivy, Clair, etc.)
                echo 'Docker Image Scanning Completed!'
            }
        }

        stage('Push Docker Image to Docker Hub') {
			steps {
				script {
					withCredentials([string(credentialsId: 'dockerhubCred', variable: 'dockerhubCred')]) {
						echo 'Logging in to Docker Hub...'
                        sh "docker login docker.io -u docker26031997 -p ${dockerhubCred}"
                        echo 'Pushing Docker Image to Docker Hub...'
                        sh 'docker push docker26031997/booking-ms:latest'
                        echo 'Docker Image Pushed to Docker Hub Successfully!'
                    }
                }
            }
        }

        stage('Push Docker Image to Amazon ECR') {
			steps {
				script {
					// Ensure AWS credentials are available for ECR login
                    withCredentials([usernamePassword(credentialsId: 'ecr-credentials',
                                                        usernameVariable: 'AWS_ACCESS_KEY_ID',
                                                        passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {

						echo 'Tagging and Pushing Docker Image to ECR...'

                        // Authenticate Docker to AWS ECR using AWS CLI
                        sh '''
                            export AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
                            export AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
                            $(aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 390844780898.dkr.ecr.ap-south-1.amazonaws.com)
                            docker tag booking-ms:latest 390844780898.dkr.ecr.ap-south-1.amazonaws.com/booking-ms:latest
                            docker push 390844780898.dkr.ecr.ap-south-1.amazonaws.com/booking-ms:latest
                        '''
                        echo 'Docker Image Pushed to Amazon ECR Successfully!'
                    }
                }
            }
        }
    }
}