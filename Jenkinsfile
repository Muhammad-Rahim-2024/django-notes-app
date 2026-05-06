@Library("SharedLibrary") _
pipeline {
    agent {label 'jenkins_agent'}
    
    stages {
        
        stage('Hello'){
            steps {
                script {
                    hello()
                }
            }
        }
        
        stage('Code') {
            steps {
                script{
                    codeClone("https://github.com/Muhammad-Rahim-2024/django-notes-app.git", "dev")
                }
            }
        }
        
        stage('Cleanup Previous Build') {
            steps {
                echo 'Stopping and removing previous containers/images...'
                // -v is optional; remove it if you want to keep your DB data
                sh 'docker compose down --rmi all --remove-orphans --volumes'
            }
        }
        
        stage('Build') {
            steps {
                echo 'This is Building the Code'
                sh 'docker build -t notes-app:latest .'
            }
        }
        
        stage('Push to DockerHub') {
            steps {
                script{
                    pushToDockerHub("DockerHubCred", "notes-app", "latest")
                }
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'This is Deploying the Code'
                // Example: stop old container and run new one
                sh 'docker ps -q --filter "name=notes-container" | xargs -r docker stop'
                sh 'docker ps -aq --filter "name=notes-container" | xargs -r docker rm'
                sh 'docker compose up -d'
                echo 'Docker Image Composed Successfull!, DEPLOYED!'
            }
        }
        
    }
    
    // THIS IS THE FOR SAVING SPACE
    post {
        always {
            echo 'Cleaning up dangling images...'
            // -f forces it, removing images that have no name/tag
            sh 'docker image prune -f'
        }
    }
}

