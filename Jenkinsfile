pipeline {
	agent any

    options {
		// Keep only the latest 3 builds and artifacts
        buildDiscarder(logRotator(numToKeepStr: '3', artifactNumToKeepStr: '3'))
    }

    tools {
		// Ensure that this tool is configured in Jenkins (Global Tool Configuration)
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
    }
}
