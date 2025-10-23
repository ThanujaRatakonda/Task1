pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('GITHUB')
        SONAR_TOKEN = credentials('SonarQube')
        ARTIFACTORY_CREDS = credentials('JFROG')
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/ThanujaRatakonda/Task1.git', credentialsId: 'GITHUB'
            }
        }

        stage('Build with Ant') {
            steps {
                sh 'ant clean build'
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SONAR_HOST_URL = 'http://10.131.103.92:9000'
            }
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh "sonar-scanner -Dsonar.projectKey=Task1 -Dsonar.sources=. -Dsonar.host.url=${SONAR_HOST_URL} -Dsonar.login=${SONAR_TOKEN}"
                }
            }
        }

        stage('Upload to Artifactory') {
            steps {
                script {
                    def server = Artifactory.newServer(
                        url: 'http://10.131.103.92:8081/artifactory',
                        username: ARTIFACTORY_CREDS_USR,
                        password: ARTIFACTORY_CREDS_PSW
                    )

                    def uploadSpec = """{
                      "files": [{
                        "pattern": "target/*.jar",
                        "target": "libs-release-local/Task1/"
                      }]
                    }"""

                    server.upload(uploadSpec)
                }
            }
        }
    }
}
