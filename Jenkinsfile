pipeline {
    agent any

    tools {
        jdk 'Java'
        ant 'ant'
    }

    environment {
        ARTIFACTORY_URL = 'http://10.131.103.92:8081/artifactory'
        ARTIFACTORY_REPO = 'libs-release-local'
    }

    stages {
        stage('Cleanup') {
            steps {
                deleteDir()
            }
        }

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
            options {
                timeout(time: 5, unit: 'MINUTES')
            }
            steps {
                withCredentials([string(credentialsId: 'SonarQube', variable: 'SONAR_TOKEN')]) {
                    withSonarQubeEnv('SonarQube') {
                        sh '''
                            sonar-scanner \
                            -Dsonar.projectKey=Task1 \
                            -Dsonar.sources=. \
                            -Dsonar.token=$SONAR_TOKEN \
                            -Dsonar.host.url=http://10.131.103.92:9000
                        '''
                    }
                }
            }
        }

        stage('Upload to Artifactory') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'JFROG', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh '''
                        FILE_NAME=$(basename dist/*.jar)
                        curl -u $USERNAME:$PASSWORD -T dist/$FILE_NAME "$ARTIFACTORY_URL/$ARTIFACTORY_REPO/Task1/$FILE_NAME"
                    '''
                }
            }
        }
    }
}
