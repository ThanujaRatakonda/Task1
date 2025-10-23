pipeline {
    agent any

    tools {
        jdk 'Java'
        ant 'ant'
    }

    environment {
        ARTIFACTORY_URL = 'http://10.131.103.92:5040/artifactory' // Corrected port to match your setup
        ARTIFACTORY_REPO = 'libs-release-local'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM',
                    userRemoteConfigs: [[
                        url: 'https://github.com/ThanujaRatakonda/Task1.git',
                        credentialsId: 'GitHub Personal Access Token' // ✅ Use exact credential ID
                    ]],
                    branches: [[name: '*/main']]
                ])
            }
        }

        stage('Build') {
            steps {
                sh 'ant clean dist' // ✅ Matches your build.xml default target
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh "sonar-scanner -Dsonar.projectKey=Task1 -Dsonar.sources=. -Dsonar.login=${SONAR_TOKEN}"
                }
            }
        }

        stage('Upload to Artifactory') {
            steps {
                script {
                    def server = Artifactory.server('JFROG') // ✅ Use configured server ID
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
