pipeline {
    agent any
    stages {
        stage ('Build Backend') {
            steps {
                sh '/var/jenkins_home/tools/hudson.tasks.Maven_MavenInstallation/MAVEN_LOCAL/bin/mvn clean package -DskipTests=true'
            }
        }
    }
}