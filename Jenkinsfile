@Library("sharedLib") _
pipeline {
    agent { label "ultimate" }

    stages {
        stage("Shared Lib Runs") {
            steps {
                script {
                    pipeline_script()
                }
            }
        }

        stage("Clean Workspace") {
            steps {
                cleanWs()
                sh '''
                    mkdir -p ./data/mysql/db
                    chmod 777 ./data/mysql/db
                '''
            }
        }

        stage("Code") {
            steps {
                script {
                    gitCloner("https://github.com/brijesh025/devops-notes-app.git", "main")
                }
            }
        }

        stage("Build") {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "dockerHubPass",
                    passwordVariable: "dockerHubPassword",
                    usernameVariable: "dockerHubUsername"
                )]) {
                    echo "Building the Docker image..."
                    sh "whoami"
                    script {
                        docker_build("notes-app", "latest", "${dockerHubUsername}")
                    }
                }
            }
        }

        stage("Test") {
            steps {
                echo "Running tests..."
            }
        }

        stage("Push to Docker Hub") {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "dockerHubPass",
                    passwordVariable: "dockerHubPassword",
                    usernameVariable: "dockerHubUsername"
                )]) {
                    echo "Pushing image to Docker Hub..."
                    sh "docker login -u ${dockerHubUsername} -p ${dockerHubPassword}"
                    sh "docker image tag notes-app:latest ${dockerHubUsername}/notes-app:latest"
                    sh "docker push ${dockerHubUsername}/notes-app:latest"
                }
            }
        }

        stage("Deploying") {
            steps {
                echo "Deploying the application..."
                sh 'docker compose up -d'
            }
        }
    }
}
