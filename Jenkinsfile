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
                    app = docker.build("ankitjethi/train-schedule")
                    app.inside {
                        sh 'npm test'
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
                        app.push("${env.BUILD_NUMBER}")
                        app.push('latest')
                    }
                }
            }
        }
        stage('Deploy-To-Production') {
            when {
                branch 'master'
            }
            input 'Do you want to deploy to Production?'
            milestone(1)
            steps {
                withCredentials([usernamePassword(credentialsId: 'webserver_login', passwordVariable: 'USERPASS', usernameVariable: 'USERNAME')]) {
                    sh 'sshpass -p $USERPASS ssh $USERNAME@$prod_ip "docker pull ankitjethi/train-schedule:${env.BUILD_NUMBER}'
                }
            }
        }
    }
}
