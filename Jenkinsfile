pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build-Docker-Image') {
            when {
                branch 'master'
            }
            steps {
                //sh 'docker build -t ankitjethi/train-schedule .'
                script {
                    def app = docker.build("ankitjethi/train-schedule:${env.BUILD_NUMBER}")
                    app.inside {
                        sh 'echo $(curl localhost:8080)'
                    }
                }
            }
        }
        stage('Publish-Docker-Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    docker.withRegistry ('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push('latest')
                    }
                }
            }
        }
    }
}
