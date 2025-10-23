pipeline {
    agent any

    tools {
        jdk 'Java'
        ant 'ant'
    }

    environment {
        ARTIFACTORY_REPO = 'libs-release-local'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM',
                    userRemoteConfigs: [[
                        url: 'https://github.com/ThanujaRatakonda/Task1.git',
                        credentialsId: 'GITHUB'
                    ]],
                    branches: [[name: '*/master']]
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
                        sh 'sonar-scanner -Dsonar.projectKey=Task1 -Dsonar.sources=. -Dsonar.token=${SONAR_TOKEN} -Dsonar.host.url=http://10.131.103.92:9000'
                    }
                }
            }
        }

        stage('Upload to Artifactory') {
            steps {
                rtUpload (
                    serverId: 'JFROG', // ✅ Make sure this matches your configured Server ID
                    spec: """{
                        "files": [{
                            "pattern": "dist/*.jar",
                            "target": "${ARTIFACTORY_REPO}/Task1/"
                        }]
                    }"""
                )
            }
        }
    }
}
