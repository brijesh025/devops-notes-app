@Library("sharedLib") _
pipeline{
    agent {label "ultimate"}
    
    stages{
        stage("Shared Lib Runs"){
            steps{
                script{
                    pipeline_script()
                }
            }
        }
        stage("Clean Workspace"){
            steps{
                cleanWs()
                 sh '''
                        mkdir -p ./data/mysql/db
                        chmod 777 ./data/mysql/db
                    '''
            }
        }
        stage("Code"){
            steps{
                script{
                    gitCloner("https://github.com/brijesh025/devops-notes-app.git", "main")
                }
            }
        }
        stage("Build"){
            steps{
                echo "This is building the code"
                sh "whoami"
                script{
                    docker_build("notes-app", "latest", "${dockerHubUsername}")
                }
            }
        }
        stage("Test"){
            steps{
                echo "This is testing the code"
            }
        }
        stage("Push to docker hub"){
            steps{
                echo "This is pussying to docker hub"
                withCredentials([usernamePassword('credentialsId': "dockerHubPass" ,
                    passwordVariable: "dockerHubPassword", 
                    usernameVariable: "dockerHubUsername")]){
                        sh "docker login -u ${env.dockerHubUsername} -p ${env.dockerHubPassword}"
                        sh "docker image tag notes-app:latest ${env.dockerHubUsername}/notes-app:latest"
                        sh "docker push ${env.dockerHubUsername}/notes-app:latest"
                    }
            }
        }
        stage("Deploying"){
            steps{
                echo "This is deployment process going on and created a ultimate jenkins_learner folder"
                sh 'docker compose up -d'
                }
            }
        }
    }
