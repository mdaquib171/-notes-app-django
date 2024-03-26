# Simple Notes App

This is a simple notes app built with React and Django.

## Requirements

- Python 3.9
- Node.js
- React

## Installation

1. Clone the repository

    ```bash
    git clone https://github.com/mohdaquib171/notes-app-django.git
    ```

2. Build the app

    ```bash
    docker build -t notes-app .
    ```

3. Run the app

    ```bash
    docker run -d -p 8000:8000 notes-app:latest
    ```

## Setting up Jenkins Declarative Pipeline

To set up a Jenkins declarative pipeline for this project, follow these steps:

1. Create an EC2 instance and connect to it using SSH or any other method.
2. Update the system by running the command:

    ```bash
    sudo apt-get update
    ```

3. Clone the repository from GitHub:

    ```bash
    git clone https://github.com/mohdaquib171/notes-app-django.git
    ```

4. Install Docker:

    ```bash
    sudo apt install docker.io
    ```

5. Add the Ubuntu user to the Docker group:

    ```bash
    sudo usermod -aG docker $USER
    ```

6. Reboot the system:

    ```bash
    sudo reboot
    ```

7. Build the Docker image:

    ```bash
    docker build -t notes-app .
    ```

8. Install Jenkins:

    - Update the package list:

        ```bash
        sudo apt-get update
        ```

    - Install Java:

        ```bash
        sudo apt install openjdk-17-jre
        ```

    - Add Jenkins repository key:

        ```bash
        curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
        ```

    - Install Jenkins:

        ```bash
        sudo apt install jenkins
        ```

    - Start Jenkins:

        ```bash
        sudo systemctl start Jenkins
        ```

9. Configure the firewall to allow access to port 8080.

10. Setup Jenkins:

    - Get the initial admin password:

        ```bash
        sudo cat /var/lib/jenkins/secrets/initialAdminPassword
        ```

    - Install suggested plugins.

11. Create a Docker Hub credential in Jenkins.

12. Install Docker Compose:

    ```bash
    sudo apt-get install docker-compose
    ```

13. Add Jenkins to the Docker group:

    ```bash
    sudo usermod -aG docker jenkins
    ```

14. Reboot the system:

    ```bash
    sudo reboot
    ```

15. Configure the pipeline in Jenkins:

    - Create a new item and select "Pipeline".
    - Configure the GitHub project URL.
    - Add a GitHub webhook for automatic triggering of builds.

16. Use the following Jenkinsfile for the pipeline:

    ```groovy
    pipeline {
        agent any 
        
        stages {
            stage("Clone Code") {
                steps {
                    echo "Cloning the code"
                    git url:"https://github.com/mohdaquib171/notes-app-django.git", branch: "main"
                }
            }
            stage("Build") {
                steps {
                    echo "Building the image"
                    sh "docker build -t my-note-app ."
                }
            }
            stage("Push to Docker Hub") {
                steps {
                    echo "Pushing the image to docker hub"
                    withCredentials([usernamePassword(credentialsId:"dockerHub",passwordVariable:"dockerHubPass",usernameVariable:"dockerHubUser")]){
                    sh "docker tag my-note-app ${env.dockerHubUser}/my-note-app:latest"
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker push ${env.dockerHubUser}/my-note-app:latest"
                    }
                }
            }
            stage("Deploy") {
                steps {
                    echo "Deploying the container"
                    // sh "docker run -d -p 8000:8000 mohdaquib/my-note-app:latest"
                    sh "docker-compose down && docker-compose up -d"
                }
            }
        }
    }
    ```

17. Set up the GitHub webhook to automatically start the Jenkins pipeline whenever there are changes to the code.
