pipeline { // This is my personal test to try all labs in one place
    agent any
    stages {
        stage ('Checkout') {
            steps {
                git branch:'master', url: 'https://github.com/OWASP/Vulnerable-Web-Application.git'
            }
        }

		stage('OWASP DependencyCheck') {
			steps {
				dependencyCheck additionalArguments: '--noupdate --format HTML --format XML ', odcInstallation: 'OWASP DependencyCheck'
			}
		}

        // stage('Code Quality Check via SonarQube') {
        //     steps {
        //         script {
        //         def scannerHome = tool 'SonarQube';
        //             withSonarQubeEnv('SonarQube') {
        //             sh "/var/jenkins_home/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQube/bin/sonar-scanner -Dsonar.projectKey=OWASP -Dsonar.sources=. -Dsonar.host.url=http://192.168.18.5:9000 -Dsonar.token=sqp_e9db62f8651602d9681fe664464d84d157e771bf"
        //             }
        //         }
        //     }
        // }
    }
    // post {
    //     always {
    //         recordIssues enabledForFailure: true, tool: sonarQube()
    //     }
    // }

    	post {
		success {
			dependencyCheckPublisher pattern: 'dependency-check-report.xml'
		}
	}
}
