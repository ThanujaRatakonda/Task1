pipeline {
    agent any

    tools {
        jdk 'Java'
        ant 'ant'
    }

    environment {
        ARTIFACTORY_URL = 'http://10.131.103.92:8081/artifactory' // ✅ Corrected Artifactory port
        ARTIFACTORY_REPO = 'libs-release-local'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM',
                    userRemoteConfigs: [[
                        url: 'https://github.com/ThanujaRatakonda/Task1.git',
                        credentialsId: 'GITHUB' // ✅ Ensure this matches your Jenkins credential ID
                    ]],
                    branches: [[name: '*/master']] // ✅ Adjust if your repo uses 'main'
                ])
            }
        }

        stage('Build') {
            steps {
                sh 'ant clean dist'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'SonarQube', variable: 'SONAR_TOKEN')]) {
                    withSonarQubeEnv('SonarQube') {
                        sh "sonar-scanner -Dsonar.projectKey=Task1 -Dsonar.sources=. -Dsonar.host.url=http://10.131.103.92:9000 -Dsonar.login=${SONAR_TOKEN}"
                    }
                }
            }
        }

        stage('Upload to Artifactory') {
            steps {
                script {
                    def server = Artifactory.server('JFROG') // ✅ Server ID configured in Jenkins
                    def uploadSpec = """{
                        "files": [{
                            "pattern": "dist/*.jar",
                            "target": "${ARTIFACTORY_REPO}/Task1/"
                        }]
                    }"""
                    server.upload(uploadSpec)
                }
            }
        }
    }
}
