pipeline {
    agent any

    stages {
        stage('Pull Code') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/${branch}']], extensions: [], userRemoteConfigs: [[credentialsId: 'Github', url: 'https://github.com/ryan4456/jenkins_demo.git']]])
            }
        }
        stage('SonarQube Check') {
            steps {
                script {
                    scannerHome = tool 'sonar-scanner'
                }
                withSonarQubeEnv('SonarQubeServer') {
                    bat "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
        stage("Build Project") {
            steps {
                bat 'mvn clean package'
            }
        }
        stage("Deploy to Dev"){
            steps {
                deploy adapters: [tomcat8(credentialsId: 'Tomcat_Dev', path: '', url: 'http://54.252.251.169:8080')], contextPath: null, onFailure: false, war: 'target/*.war'
            }
        }
    }

    post {
        always {
            emailext(
                subject: 'Build Notification ${PROJECT_NAME} - Build # ${BUILD_NUMBER} - ${BUILD_STATUS}',
                body: '${FILE, path="email.html"}',
                to: 'ryan.li.2021@outlook.com'
            )
        }
    }
}
