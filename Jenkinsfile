pipeline {
    // This is my personal test to try all labs in one place
    agent any
    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/OWASP/Vulnerable-Web-Application.git'
            }
        }

        stage('OWASP DependencyCheck') {
            steps {
                dependencyCheck additionalArguments: '--noupdate --format HTML --format XML', odcInstallation: 'OWASP DependencyCheck'
            }
        }

        stage('Build') {
            steps {
                sh '/var/jenkins_home/apache-maven-3.9.8/bin/mvn --batch-mode -V -U -e clean verify -Dsurefire.useFile=false -Dmaven.test.failure.ignore'
            }
        }

        stage('Analysis') {
            steps {
                sh '/var/jenkins_home/apache-maven-3.9.8/bin/mvn --batch-mode -V -U -e checkstyle:checkstyle pmd:pmd pmd:cpd findbugs:findbugs'
            }
        }

        // Uncomment and configure this stage if you have SonarQube set up
        // stage('Code Quality Check via SonarQube') {
        //     steps {
        //         script {
        //             def scannerHome = tool 'SonarQube'
        //             withSonarQubeEnv('SonarQube') {
        //                 sh "/var/jenkins_home/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQube/bin/sonar-scanner -Dsonar.projectKey=OWASP -Dsonar.sources=. -Dsonar.host.url=http://192.168.18.5:9000 -Dsonar.token=sqp_e9db62f8651602d9681fe664464d84d157e771bf"
        //             }
        //         }
        //     }
        // }
    }

    post {
        always {
            junit testResults: '**/target/surefire-reports/TEST-*.xml'
            recordIssues enabledForFailure: true, tools: [mavenConsole(), java(), javaDoc()]
            recordIssues enabledForFailure: true, tool: checkStyle()
            recordIssues enabledForFailure: true, tool: spotBugs(pattern: '**/target/findbugsXml.xml')
            recordIssues enabledForFailure: true, tool: cpd(pattern: '**/target/cpd.xml')
            recordIssues enabledForFailure: true, tool: pmdParser(pattern: '**/target/pmd.xml')
        }

        success {
            dependencyCheckPublisher pattern: 'dependency-check-report.xml'
        }
    }
}
