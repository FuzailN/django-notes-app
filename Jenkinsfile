pipeline {
    agent any 
    
    stages {
        stage("Clone Code") {
            steps {
                echo "Cloning the code"
                git branch: 'main', url: 'https://github.com/LondheShubham153/django-notes-app.git'
            }
        }
        
        stage("Build") {
            steps {
                echo "Building the image"
                sh "docker build -t my-note-app ."
            }
        }
        
        stage("SonarQube Analysis") {
            steps {
                script {
                    def scannerHome = tool name: 'sonar-scanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
                    withEnv(["PATH+SCANNER=${scannerHome}/bin"]) {
                        sh """sonar-scanner \
                            -Dsonar.projectKey=django \
                            -Dsonar.sources=. \
                            -Dsonar.host.url=http://4.224.52.255:9000 \
                            -Dsonar.login=sqp_640bac49ee820d89e8d80d22ce5f58486b177cb6
                        """
                    }
                }
            }
        }
        
        stage("Push to Docker Hub") {
            steps {
                echo "Pushing the image to docker hub"
                withCredentials([usernamePassword(credentialsId:"dockerHub",passwordVariable:"dockerHubPass",usernameVariable:"dockerHubUser")]) {
                    sh "docker tag my-note-app ${env.dockerHubUser}/my-note-app:latest"
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker push ${env.dockerHubUser}/my-note-app:latest"
                }
            }
        }
        
        stage("Deploy") {
            steps {
                echo "Deploying the container"
                sh "docker-compose down && docker-compose up -d"
            }
        }
    }
}
