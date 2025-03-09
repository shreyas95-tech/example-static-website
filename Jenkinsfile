pipeline {
    agent any

    environment {
        ARTIFACTORY_SERVER = 'artifactory' // Your JFrog Server ID
        ARTIFACTORY_REPO = 'static-website-repo' // Your JFrog Repo Name
        DEPLOY_PATH = '/var/www/html/static-website'
    }

    stages {
        stage('Checkout Code from GitHub') {
            steps {
                git branch: 'main', url: 'https://github.com/shreyas95-tech/example-static-website.git'
            }
        }

        stage('Build Static Website') {
            steps {
                sh 'zip -r static-website.zip *'
            }
        }

        stage('Upload to JFrog Artifactory') {
            steps {
                script {
                    def server = Artifactory.server(ARTIFACTORY_SERVER)
                    def uploadSpec = """{
                        "files": [{
                            "pattern": "static-website.zip",
                            "target": "${ARTIFACTORY_REPO}/version-1.0/"
                        }]
                    }"""
                    server.upload(uploadSpec)
                }
            }
        }

        stage('Deploy to Web Server') {
            steps {
                sh '''
                sudo mkdir -p ${DEPLOY_PATH}
                sudo unzip -o static-website.zip -d ${DEPLOY_PATH}
                sudo systemctl restart apache2
                '''
            }
        }
    }
}
