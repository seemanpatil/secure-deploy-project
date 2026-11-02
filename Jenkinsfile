
pipeline {
    agent any

    environment {
        IMAGE_NAME = 'secure-deploy-backend'
        IMAGE_TAG = 'latest'
        DOCKER_EXE = 'C:\\Users\\User\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe'
        TRIVY_EXE = 'C:\\Users\\User\\AppData\\Local\\Microsoft\\WinGet\\Packages\\AquaSecurity.Trivy_Microsoft.Winget.Source_8wekyb3d8bbwe\\trivy.exe'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Maven Build and Tests') {
            steps {
                bat 'mvn -f backend/pom.xml clean verify'
            }
        }

        stage('Docker Build') {
            steps {
                bat '"%DOCKER_EXE%" build -t %IMAGE_NAME%:%IMAGE_TAG% backend'
            }
        }

        stage('Trivy Vulnerability Scan') {
            steps {
                bat '"%TRIVY_EXE%" image --image-src docker --scanners vuln --timeout 15m --format json --output trivy-report.json %IMAGE_NAME%:%IMAGE_TAG%'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'trivy-report.json',
                allowEmptyArchive: true
        }
        success {
            echo 'Secure Deploy pipeline completed successfully.'
        }
        failure {
            echo 'Pipeline failed. Review the Jenkins console output.'
        }
    }
}
