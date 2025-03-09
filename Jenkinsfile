pipeline {
    agent any

    environment {
        ARTIFACTORY_SERVER = 'Artifactory' // Your JFrog Server ID
        ARTIFACTORY_REPO = 'static-website-repo' // Your JFrog Repo Name
        DEPLOY_PATH = '/var/www/html/static-website'
        SERVER_USER = credentials('web-server-creds').username
        SERVER_PASSWORD = credentials('web-server-creds').password
        SERVER_IP = '13.201.31.90' // Your EC2 Instance IP
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
                    def uploadSpec = """
                    {
                        "files": [{
                            "pattern": "static-website.zip",
                            "target": "${ARTIFACTORY_REPO}/version-1.0/"
                        }]
                    }
                    """
                    server.upload(uploadSpec)
                }
            }
        }

        stage('Deploy to Web Server') {
            steps {
                sh """
                sshpass -p '$SERVER_PASSWORD' ssh -o StrictHostKeyChecking=no $SERVER_USER@$SERVER_IP << EOF
                    sudo mkdir -p $DEPLOY_PATH
                    sudo rm -rf $DEPLOY_PATH/*
                    sudo unzip static-website.zip -d $DEPLOY_PATH
                    sudo systemctl restart apache2
                EOF
                """
            }
        }
    }
}
