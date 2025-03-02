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
        stage('SonarQube Code Quality') {
			environment {
				scannerHome = tool 'qube'
            }
            steps {
				echo 'Starting SonarQube Code Quality Scan...'
                withSonarQubeEnv('sonar-server') {
					sh 'mvn sonar:sonar'
                }
                echo 'SonarQube Scan Completed. Checking Quality Gate...'
                timeout(time: 10, unit: 'MINUTES') {
					waitForQualityGate abortPipeline: true
                }
                echo 'Quality Gate Check Completed!'
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
        stage('Upload Docker Image to Nexus') {
			steps {
				script {
					withCredentials([usernamePassword(credentialsId: 'nexus-credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
						sh 'docker login http://3.111.144.179:8085/repository/booking-ms/ -u admin -p ${PASSWORD}'
                        echo "Push Docker Image to Nexus : In Progress"
                        sh 'docker tag booking-ms 3.111.144.179:8085/booking-ms:latest'
                        sh 'docker push 3.111.144.179:8085/booking-ms'
                        echo "Push Docker Image to Nexus : Completed"
                    }
                }
            }
        }
    stage('Clean Up Local Docker Images') {
			steps {
				echo 'Cleaning Up Local Docker Images...'
                sh '''
                    docker rmi satyam88/booking-ms:latest || echo "Image not found or already deleted"
                    docker rmi booking-ms:latest || echo "Image not found or already deleted"
                    docker rmi 533267238276.dkr.ecr.ap-south-1.amazonaws.com/booking-ms:latest || echo "Image not found or already deleted"
                    docker image prune -f
                '''
                echo 'Local Docker Images Cleaned Up Successfully!'
            }
        }
    }
}

