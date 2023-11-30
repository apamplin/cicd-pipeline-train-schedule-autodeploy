pipeline {
    agent any
    environment {
        DOCKER_IMAGE_NAME = "apamplin/train-schedule"
    }
    stages {
        stage("Checkout from github repo"){
            steps{
            git url: 'https://github.com/apamplin/cicd-pipeline-train-schedule-autodeploy.git'
            }
        }
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew wrapper --gradle-version=8.5'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('DeployToProduction') {
            steps {
                kubeconfig(caCertificate: 'MIIDDzCCAfegAwIBAgIUcqMRLNCJtnpY2C3moIORgjgrBvgwDQYJKoZIhvcNAQELBQAwFzEVMBMGA1UEAwwMMTAuMTUyLjE4My4xMB4XDTIzMTEwODE2NTMxMVoXDTMzMTEwNTE2NTMxMVowFzEVMBMGA1UEAwwMMTAuMTUyLjE4My4xMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAqeURtgTbnDapLc7vO1XxvBbRzdklWwTkxiwDOYN3MK2YYsvIGaGbBJaGbPYFzmOvWlNHiMeGLieUTytbFOdZYaqmcdIJQySK/lM2o5EjC93MNESOifhcylFSB7YSbt3Dz54DUVI/RYaJniiYGIpezvG3xPMQN+Npol3rfp73uztD7IiM4lSPb7Ji8jXZTjR5Kwg6mZaph5EDuhLFrDDzRKveQHxhHIGyHjP8YxHhe5+Y1fpq1cGEdGNsslLBKv6kzZytgP7acOlK2kXzBtUVPHYwzkAlBMQ1SDHimy1jKFbpr/2r0x5PzTi0gFkzh7Dd2TFaLmisU7WOBBAD5lHhNwIDAQABo1MwUTAdBgNVHQ4EFgQUrlF10gaVsaIAcvwyyCK6u5Pe0jkwHwYDVR0jBBgwFoAUrlF10gaVsaIAcvwyyCK6u5Pe0jkwDwYDVR0TAQH/BAUwAwEB/zANBgkqhkiG9w0BAQsFAAOCAQEAPcxiUlQy/ZjKyqq1DtgLBrklLY3TwwKlCX9pfQtVjIC/Xw1K8rXqpHa/QQySfO657ES0Q85/El7di/2lyYyCh5TDo+k2xSzFXxwov/FBh7Tr+XWNtzjAFOgzcknMTy0zQPmHnM0pu9dZkPet5UiX6A/tIaiWQ7HvpnRj0avGmyPuOt7P3SpEf2DMDtAfReoLiao8EYmX65HhCQ//qAbJp5jAVPZyUfa5Lx5cneUlQvLD8MdB61s1bFiZrluQOF5SUEc73ikFmgN+rVGE9La4E6Q3ScZV8VoEwcqiKlJhijtULSI1VxkYJbleMgM+fp3pKbuBvlobTCVX9kDEftitqg==', credentialsId: '2ae22ff8-baf1-4ec5-96d1-4e436a88ff0a', serverUrl: 'https://10.4.8.52:16443') {
    // some block
                 sh 'kubectl apply -f deployment.yaml'
                 sh 'kubectl apply -f app-service.yaml'
                 sh 'kubectl rollout restart deployment train-schedule'
}
            }
        }
    }
}
