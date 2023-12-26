pipeline {
    agent any
    tools {
  maven 'maven'
  git 'Default'
}
    stages {
        stage ('clone job') {
            steps {
              git 'https://github.com/Sid1545/webapp.git'
            }
        }
        stage ('build') {
            steps {
              sh 'mvn clean install'
            }
        }
        stage ('deploy') {
            steps {
              sshagent(['user']) {
              sh 'scp -o StrictHostKeyChecking=no /var/lib/jenkins/workspace/pipe/target/app.war ec2-user@18.234.165.178:/home/ec2-user/apache-tomcat-8.5.97/webapps'
              }
            }
        }
        stage ("build & SonarQube analysis") {
            steps {
                withSonarQubeEnv ('sonar') {
                sh 'mvn sonar:sonar'
                }
            }
        }
        stage('JFrog') {
            steps {
                script {
                    def server = Artifactory.server 'JFrog'
                    def uploadSpec = '''{
                        "files": [
                            {
                                "pattern": "http://44.214.15.1:8082/artifactory",
                                "target": "maven/"
                            }
                        ]
                    }'''
                    server.upload(uploadSpec)
                }
            }
        }
    }
}
