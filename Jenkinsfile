pipeline {
    agent any

    stages {
        stage('Upload to Artifactory') {
            steps {
                script {
                    // Initialize Artifactory server
                    def server = Artifactory.server 'JFROG' // This should match your credential ID

                    // Define upload spec
                    def uploadSpec = """{
                      "files": [{
                        "pattern": "target/*.jar",
                        "target": "libs-release-local/my-app/"
                      }]
                    }"""

                    // Upload artifacts
                    server.upload(uploadSpec)
                }
            }
        }
    }
}
