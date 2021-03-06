pipeline {
    agent any
    environment {
                scannerHome = tool 'SONAR_SCANNER'
                mavemHome = tool 'MAVEN_LOCAL'
            }
    stages {
        stage ('Build Backend') {
            steps {
                sh "${mavemHome}/bin/mvn clean package -DskipTests=true"
            }
        }
        stage ('Unit Tests') {
            steps {
                sh "${mavemHome}/bin/mvn test"
            }
        }
        stage ('Sonar Analysis') {
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
        stage ('Deploy tomcat') {
            steps {
                deploy adapters: [tomcat8(credentialsId: 'Tomcat', path: '', url: 'http://192.168.0.2:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }
        stage ('API Test') {
            steps {
                dir('api-test'){
                    git branch: 'main', url: 'https://github.com/wendelguedes/tasks-api-test.git'
                    sh "${mavemHome}/bin/mvn test"
                }
            }
        }
        stage ('Deploy Frontend') {
            steps {
                dir('frontend'){
                    git branch: 'master', url: 'https://github.com/wendelguedes/tasks-frontend.git'
                    sh "${mavemHome}/bin/mvn clean package -DskipTests=true"
                    deploy adapters: [tomcat8(credentialsId: 'Tomcat', path: '', url: 'http://192.168.0.2:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
                }
            }
        }
        stage ('Functional Test') {
            steps {
                dir('functional-test'){
                    git branch: 'main', url: 'https://github.com/wendelguedes/tasks-functional-tests.git'
                    sh "${mavemHome}/bin/mvn test"
                }
            }
        }
        stage ('Build production') {
            steps {
                dir('functional-test'){
                    sh "docker-compose build"
                    sh "docker-compose up -d"
                }
            }
        }
    }
}

