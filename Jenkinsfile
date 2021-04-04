pipeline {
    agent any
    stages {
        stage ('Build Backend') {
            steps {
                sh '/var/jenkins_home/tools/hudson.tasks.Maven_MavenInstallation/MAVEN_LOCAL/bin/mvn clean package -DskipTests=true'
            }
        }
        stage ('Unit Tests') {
            steps {
                sh '/var/jenkins_home/tools/hudson.tasks.Maven_MavenInstallation/MAVEN_LOCAL/bin/mvn test'
            }
        }
        stage ('Sonar Analysis') {
            environment {
                scannerHome = tool 'SONAR_SCANNER'
            }
            steps {
                withSonarQubeEnv('SONAR_LOCAL'){
                    sh "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://192.168.0.2:9000 -Dsonar.login=4e76078fb4a622c16af38aec91da049650b3115f -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java"
                }
            }
        }
        stage ('Quality Gate') {
            steps {
                sleep(5)
                timeout(time: 1, unit: 'MINUTES'){
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
